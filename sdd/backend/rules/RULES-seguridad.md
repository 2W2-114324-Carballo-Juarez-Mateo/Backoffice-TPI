# RULES — Seguridad

1. **Autorización en dos niveles:** el microservicio revalida identidad, rol, permisos y alcance. **Nunca** confíes solo en el Gateway.
2. **No confiar en el frontend:** una restricción administrativa se valida en backend. "El frontend oculta el botón" ≠ "operación imposible".
3. **Secrets fuera del repo:** `DATABASE_PASSWORD`, `JWT_SECRET`, `KAFKA_BOOTSTRAP_SERVERS`, `BREAK_GLASS_SECRET` → variables de entorno/secret manager. Nunca en código ni en `application.yml`.
4. **Contraseñas con bcrypt;** JWT firmado; 2FA (TOTP) obligatorio.
5. **No exponer:** stack traces, secretos, SQL, prompts internos, claves API, información de otros tenants.
6. **Rate limiting en Gateway** (429 con `Retry-After`) + **Idempotency Keys** en operaciones críticas (PUT config, baja de ADMIN).
7. **Break-glass server-only:** la recuperación de ADMIN no es un endpoint HTTP.
8. **Logs:** nunca loguear contraseñas, tokens ni PII innecesaria; correlacionar con `correlationId`.