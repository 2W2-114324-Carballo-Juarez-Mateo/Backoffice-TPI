---
slug: api-gateway-contract-for-services-behind-it
title: API Gateway contract for services behind it
description: What the gateway guarantees to a routed service, the identity headers it injects, the RFC
  9457 error shape, and where authorization stops being its job.
when_to_use: Use when exposing a service through the API gateway, reading X-User-Id or X-Principal-Type
  headers, handling problem+json errors, calling another microservice, or getting 404/401/403 from the
  gateway.
stack: shared
type: contract
owning_team: Identidad y Usuarios
version: 3
tags:
- allowlist
- authorization
- errors
- gateway
- headers
- identity
- problem-json
- rfc9457
- routing
- service-token
---

## Rule

Everything a service behind the gateway knows about its caller arrives in HTTP headers. **Never parse the JWT** â the gateway already validated it. And **never authorize by role in the gateway**: it authenticates, your service authorizes.

## Your name, your prefix, your ports

Your service id comes from your repository: repo `tpi-{name}` â `spring.application.name = {name}-service` â routed at `/api/{name}/**`. `tpi-cursos` â `cursos-service` â `/api/cursos/**`. The same string is your Eureka id, your allowlist entry, your `aud` in a service token and your public prefix: pick it once.

Keep that prefix in properties, never as a literal in a controller:

```yaml
app:
  api:
    public-path: /api/{name}/public
    private-path: /api/{name}
```

```java
@RequestMapping("${app.api.private-path}/subjects")   // authenticated
@RequestMapping("${app.api.public-path}/catalog")     // anonymous
```

Fixed ports: the gateway is always **8080** (management **8081**) and Eureka listens on **8761**. Your service publishes no port at all â `expose:` on the `tpi-platform` network â and picks its own traffic/management pair (users-service uses 8082/8083), agreed beforehand so two teams do not take the same one.

## The five identity headers

| Header | Person token | Service token |
|---|---|---|
| `X-Principal-Type` | `user` | `service` |
| `X-User-Id` | the `sub` (UUID) | â |
| `X-User-Roles` | roles, comma-separated | â |
| `X-Service-Id` | â | the `sub` (client id) |
| `X-Service-Scopes` | â | roles (`MS`) first, then scopes |

Format: comma, no space, no duplicates, no trailing comma. `ADMIN,PROFESSOR`.

Roles in use: `ADMIN`, `GESTOR`, `PROFESSOR`, `STUDENT`, plus `MS` for service tokens only. Do not hardcode the list â check the ones your domain needs and treat the rest as unauthorized.

**Why you can trust them:** the gateway strips all five on *every* request, public ones included, and only then injects values derived from the validated token. A spoofed `X-User-Roles: ADMIN` is deleted before anything is injected.

No `X-Principal-Type` means no identity: treat as anonymous, never assume a default.

## Sessions are cookies â what actually reaches you

A person's session lives in the `HttpOnly` cookie `fu_at`, so **a person's request reaches you with no `Authorization` header**. The cookie itself is forwarded â the gateway does not strip cookies â but do not read it, parse it, forward it or log it. Your identity is the headers.

A service call is the opposite: it arrives in `Authorization: Bearer`, already checked for signature and `aud`. You do not need to open that either.

Consequences for your service: no resource server, no `JwtDecoder`, no JWKS client. And when you need to call another microservice on behalf of a person, you never forward their cookie â you ask for your own service token.

## Tracing and logs

The gateway sends you `traceparent` and `X-Request-Id`, but the trace dies at your door unless you configure four things â the same ones users-service has:

1. **The tracing bridge**, next to Actuator: `io.micrometer:micrometer-tracing-bridge-otel`. With it Micrometer reads the incoming `traceparent` and hangs your spans off the gateway's span instead of starting a new trace. Never build the header by hand.
2. **`management.tracing.sampling.probability: 1.0`** â the `0.1` default leaves nine out of ten requests with no `traceId`.
3. **The log pattern with the four ids**: `%5p [${appName:-},%X{traceId:-},%X{spanId:-},%X{requestId:-}]`. Micrometer fills `traceId` and `spanId`; `requestId` is yours.
4. **One filter** (`OncePerRequestFilter`, highest precedence) that validates `X-Request-Id` against `[A-Za-z0-9._-]{1,64}`, puts it in the MDC, removes it in a `finally`, and logs one line per request â never bodies, tokens or `Authorization`.

Two rules so the trace survives a call to another service: use the **Spring-autoconfigured** `RestClient.Builder` / `WebClient.Builder` (a hand-built client propagates nothing), and forward the same `X-Request-Id` you received â the gateway preserves it when it is well formed, so one id crosses the whole chain.

## The error shape â RFC 9457

Every rejection is `application/problem+json`. **Branch on `type`, never on `title` or `detail`.**

`not-authenticated` 401 Â· `session-closed` 401 Â· `session-superseded` 401 Â· `pending-account` 403 Â· `password-change-required` 403 Â· `onboarding-pending` 403 Â· `invalid-audience` 403 Â· `access-denied` 403 Â· `route-not-found` 404 Â· `method-not-allowed` 405 Â· `too-many-attempts` 429 Â· `unexpected-error` 500 Â· `service-unavailable` 503

All prefixed `https://tpi.utn.frc/errors/`. Answer `problem+json` from your service too, so the frontend keeps one error branch instead of one per microservice.

`429` and `503` carry `Retry-After`: the gateway already says when to come back. `503` means retry â never send the user to log in again.

## Routing

Your service is exposed at `/api/{name}/**`, derived from `{name}-service`. The path is **not** rewritten: you receive the full URL.

`/api/{name}/public/**` is anonymous. That literal `public` segment is the only way to mark a route open â there is no annotation or header for it.

**Registering in Eureka does not expose you.** Discovery and exposure are separate: until your service id is on the gateway allowlist, everything returns 404. Ask the door to tell them apart, on a **private** path â `404` not on the allowlist, `503` on it with no UP instances, `401` routed.

## Registering in Eureka

The same block users-service uses, with your name and your ports. None of it is decorative:

```yaml
spring:
  application:
    name: {name}-service            # Eureka id, allowlist entry, `aud` of your tokens

server:
  port: ${SERVER_PORT:8084}         # your traffic port

management:
  server:
    port: ${MANAGEMENT_PORT:8085}   # management separate from traffic
  endpoints:
    web:
      exposure:
        include: health,info,prometheus
  endpoint:
    health:
      probes:
        enabled: true               # enables /actuator/health/readiness
      show-details: never

eureka:
  client:
    service-url:
      # compose: http://eureka:8761/eureka/   IDE: http://localhost:8761/eureka/
      defaultZone: ${EUREKA_URL:http://localhost:8761/eureka/}
    register-with-eureka: true      # without it nothing ever finds you
    fetch-registry: false           # only the gateway needs the whole registry
    healthcheck:
      enabled: true                 # publishes readiness, not just the heartbeat
  instance:
    prefer-ip-address: true         # the gateway calls you by IP

app:
  api:
    public-path: /api/{name}/public
    private-path: /api/{name}
```

| Property | Why |
|---|---|
| `spring.application.name` | Your Eureka id **and** the exact string on the allowlist. They must match or you are not routed |
| `defaultZone` | Where Eureka lives: **8761**. `http://eureka:8761/eureka/` in the compose, `localhost` from the IDE |
| `register-with-eureka: true` | You register. On `false` you start fine and nobody finds you |
| `fetch-registry: false` | You do not pull the whole registry â only the gateway needs it, because it is the one balancing |
| `healthcheck.enabled: true` | Eureka publishes your **readiness** instead of the heartbeat. Needs `health.probes.enabled: true`, or there is no readiness to publish |
| `prefer-ip-address: true` | The gateway calls you by IP; a container hostname breaks balancing as soon as the network changes |
| `management.server.port` | `/actuator/**` never leaves through the public port |

## What is guaranteed when a request reaches you

Signature, `exp` and issuer valid; the session is alive â **person tokens only**, a service token carries no `sid` and skips that check; if a person, the account is **enabled** (so you never handle account states); if a service, the token's `aud` names *your* service.

What is yours: deciding whether that principal may perform that operation.

## Authorizing in your own service

Build the `Authentication` from the headers (`ROLE_` + each value of `X-User-Roles`), leave everything private as `authenticated()`, and put the rule on each endpoint:

```java
@EnableMethodSecurity                      // without it @PreAuthorize is never evaluated

@PreAuthorize("hasRole('ADMIN')")
@PreAuthorize("hasAnyRole('ADMIN', 'GESTOR')")
@PreAuthorize("hasRole('MS')")             // another microservice only
```

`hasRole('ADMIN')` matches the authority `ROLE_ADMIN`, so the prefix is added when you build the authorities, not inside the annotation. An endpoint with no annotation is open to **any** authenticated principal â including another microservice holding a service token.

## Calling another service

Ask for your own `client_credentials` token; never reuse the person's. `audience` is mandatory and has no default: without it the token is not issued at all (`400`), and a token whose `aud` does not name the destination is rejected by the gateway with `403 invalid-audience`. One token per destination. Service tokens travel in `Authorization: Bearer`, person tokens in the `fu_at` cookie; the wrong channel is rejected.

## Limits you operate under

Timeout **3 s** â slower than that and the caller gets `503`, so make long operations async. Retry is **GET only**, so your GETs must be idempotent. Breaker opens at 50% failures over 20 calls.

See the attached file for the full guide, the onboarding checklist and symptom-based troubleshooting.
