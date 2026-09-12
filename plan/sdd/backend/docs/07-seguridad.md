# 07 — Seguridad

## Autenticación

- Usuario + contraseña + **2FA** para los roles administrativos. El 2FA actual de T01 es **OTP por email (6 dígitos)**; están evaluando TOTP/teléfono/GitHub (sin decisión ni fecha) → el front no debe asumir cómo se ve el estado 2FA.
- JWT en cookie httpOnly + Secure + SameSite (contrato definido por Identity/T01).
- **Contrato T01:** el gateway valida el JWT (RS256, JWKS `/.well-known/jwks.json`, access ~10 min); el Backoffice **nunca valida firma/exp**. Claims de persona: `sub`, `roles[]`, `type:"user"`, `jti`, `sid`, `est`, `pwd`, `onb`, `iat`, `exp`. No hay claims `permissions[]` ni `courseScope`.

## Autorización

- **Dos niveles:** Gateway (valida JWT y propaga contexto) + microservicio propietario (**autoriza localmente** con `@PreAuthorize` sobre el rol propagado). **No existe** endpoint REST de autorización en T01.
- **Contexto propagado (headers, anti-spoofing):** `X-Principal-Type`, `X-User-Id`, `X-Service-Id`, `X-User-Roles`, `X-Service-Scopes`, `traceparent` (W3C), `X-Request-Id`.
- **Roles reales:** `ADMIN` / `PROFESOR` / `ALUMNO` (+ `MS` solo service-to-service). **No existe `AUDITOR`** (lectura de auditoría = `ADMIN`).
- El alcance `course_id` **nunca** se acepta ciegamente: se valida contra la membresía real (T02). El alcance global `ALL` se **deriva server-side** del rol `ADMIN` (no viaja en token ni headers).

## Reglas de ADMIN

- Todos los ADMIN tienen los mismos permisos.
- No auto-eliminación; baja reforzada (contraseña + 2FA + confirmación escrita) — **100% validada server-side en users-service**; el Backoffice no implementa esas validaciones.
- **Protección del último ADMIN:** bloqueo incondicional (validado en T01); el Backoffice solo depende de la garantía, no la reimplementa.
- **Break-glass:** recuperación server-only (CLI, secreto de instalación, cambio de contraseña forzado, auditoría, alerta). No es un endpoint HTTP.

## Rate limiting y 429

- Rate limiting en el **Gateway** (Bucket4j in-memory o Redis RequestRateLimiter).
- Umbrales por endpoint/rol; foco en `/login`, `/api/auth/*`, `/api/users/audit`.
- Respuesta 429 con `Retry-After` + **Idempotency Keys** en operaciones críticas.

## Secretos

- Nunca en el repositorio; variables de entorno / secret manager.
- Contraseñas con bcrypt; JWT firmado RS256 (claves por entorno).

## Qué NO exponer

- Stack traces, secretos, SQL, prompts internos, claves API, información de otros tenants.

> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§18, RNF-01..03, §9).