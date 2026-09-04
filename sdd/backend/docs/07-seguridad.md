# 07 — Seguridad

## Autenticación

- Usuario + contraseña + **2FA obligatorio** (TOTP) para todos los roles.
- GitHub NO es método de login (es cuenta de trabajo para desafíos prácticos).
- JWT en cookie httpOnly + Secure + SameSite (contrato definido por Identity).

## Autorización

- **Dos niveles:** Gateway (valida JWT) + microservicio propietario (valida identidad, rol, permisos y **alcance del recurso**).
- RBAC: ADMIN / PROFESOR / ALUMNO + autorización contextual por curso (membership/ownership).
- El `tenant_id` enviado por el cliente **nunca** se acepta ciegamente: se valida contra la membresía real.

## Reglas de ADMIN

- Todos los ADMIN tienen los mismos permisos.
- No auto-eliminación; baja reforzada (contraseña + 2FA + confirmación escrita).
- **Protección del último ADMIN:** bloqueo incondicional; manejar con transacción + revalidación (concurrencia).
- **Break-glass:** recuperación server-only (CLI, secreto de instalación, cambio de contraseña forzado, auditoría, alerta). No es un endpoint HTTP.

## Rate limiting y 429

- Rate limiting en el **Gateway** (Bucket4j in-memory o Redis RequestRateLimiter).
- Umbrales por endpoint/rol; foco en `/login`, `/api/auth/*`, `/api/audit`.
- Respuesta 429 con `Retry-After` + **Idempotency Keys** en operaciones críticas.

## Secretos

- Nunca en el repositorio; variables de entorno / secret manager.
- Contraseñas con bcrypt; JWT firmado (secreto por entorno).

## Qué NO exponer

- Stack traces, secretos, SQL, prompts internos, claves API, información de otros tenants.

> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§18, RNF-01..03, §9.1).