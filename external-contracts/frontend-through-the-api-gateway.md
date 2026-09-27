---
slug: frontend-through-the-api-gateway
title: Frontend through the API Gateway
description: 'What an Angular app sends to the Gateway and how it reads the answers: session cookie, no
  tokens in JS, one problem+json branch.'
when_to_use: 'Use when an Angular app calls the API: session cookie fu_at, 401/403/429/503 handling, problem+json
  types, login redirects, CORS or withCredentials questions, tokens in localStorage.'
stack: angular
type: contract
owning_team: Identidad y Usuarios
version: 1
tags:
- angular
- auth
- cookie
- errors
- frontend
- gateway
- http
- interceptor
- problem-json
- session
---

## Rule

The browser talks only to the Gateway, same origin, and never holds a token: the session is the `HttpOnly` cookie `fu_at`, which the browser sends by itself, and every rejection is read from the `problem+json` `type`. No `Authorization` header for a person, no token in storage, no CORS, no CSRF machinery.

## What the browser sends

- Only `/api/**` on the app's own origin (nginx proxies `/api/`, `/.well-known/` and `/dev/`; everything else is the SPA). Never a microservice host, a container name or a port: the Gateway is the only door, and a direct call is also a call the service cannot trust.
- The `fu_at` cookie travels by itself: front and API share the origin, so there is no preflight and nothing to configure. Do not add CORS or `withCredentials` to "fix" local connectivity â fix the proxy instead ([[frontend-local-service-integration]]).
- Never read, parse, forward or log the cookie: it is `HttpOnly`, and its contents are not the frontend's business.
- Never store a token or roles in `localStorage`/`sessionStorage`, and never set `X-User-*`/`X-Service-*`: the Gateway strips all five reserved headers and injects its own.
- A service token in a browser is a leak, not an integration: `client_credentials` belongs to backend calls ([[micro-to-micro-calls-with-a-service-token]]).

## What comes back

Every rejection is `application/problem+json`. Branch on `type` (prefixed `https://tpi.utn.frc/errors/`), never on `title`, `detail` or the status alone, and keep the branch in one HTTP interceptor instead of scattering it per feature.

| `type` | What the app does |
|---|---|
| `not-authenticated`, `session-closed` | Session is over: go to login. |
| `session-superseded` | Someone logged in elsewhere: go to login with a message, do not auto-retry. |
| `pending-account`, `onboarding-pending`, `password-change-required` | Route to the matching screen (`pending-account` carries `accountStatus`); it is not a login failure. |
| `access-denied` | No-permission screen; the session is fine. |
| `too-many-attempts` | Read `Retry-After` and wait; never loop. Auth is rate-limited per IP (600/min; registration 10/min). |
| `service-unavailable` | `503` + `Retry-After`: retry or "try later". **Never** send the user to login. |
| `route-not-found`, `method-not-allowed` | A contract bug (wrong path or verb), not a session problem. |
| `unexpected-error` | Generic failure; report it, do not log the user out. |

The Gateway caches session state for about 3 seconds: a just-issued token can still get one `401` and a revoked one survives a moment. Treat the first `401` as the truth, but do not build retry loops around that window.

## Auth flows

Login, registration and 2FA are anonymous routes under `/api/users/public/**`; the cookie is set by users-service and the Gateway only reads it. The app never sees a JWT, so "is the user logged in?" is a question to the API (the agreed `me` endpoint), never a local decode or a flag in storage.

## Verify

- The app is served from the same origin as `/api/**`; `environment.apiUrl` carries no host from another origin.
- A dead session produces exactly one redirect to login, not a loop.
- A `503` shows "try later" and does not log the user out.
- No token, cookie or `Authorization` value in the console, storage or logs.

See [[api-gateway-contract-for-services-behind-it]] for the platform side (headers, routes, error shape) and [[frontend-local-service-integration]] for local development.