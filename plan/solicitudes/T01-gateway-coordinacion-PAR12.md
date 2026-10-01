# T01 (Gateway) · Coordinación para el consumo de PAR-12 por Accounting

> **Fecha:** 01/10/2026 · **De:** Backoffice (Tema 12) · **Para:** Tema 01 (API Gateway)
> **Motivo:** Accounting consultará `GET /api/backoffice/parameters/PAR-12` vía Gateway con autenticación servicio-a-servicio (M2M).

---

### Mensaje para T01 (Gateway)

Accounting (Tema 11) va a consultar `GET /api/backoffice/parameters/PAR-12` (parámetro global de vidas) a través del Gateway con autenticación servicio-a-servicio (M2M). Backoffice autoriza por los **headers que inyecta el Gateway** (no valida firmas). Necesitamos confirmar/configurar:

**1. `X-Service-Id` canónico**

El valor exacto que va a propagar el Gateway en las llamadas de Accounting. El contrato publicado hoy referencia `tema-08-banco`; confirmar si es ese u otro valor literal.

**2. Scope de lectura**

El Gateway debe propagar el scope **`backoffice.parameters.read`** (Backoffice lo acepta como authority). Confirmar el nombre exacto y que viaja en `X-Service-Scopes`.

**3. Headers esperados** en cada request de servicio a `/api/backoffice/parameters/**`

```
X-Principal-Type: service
X-Service-Id: <id de Accounting>
X-Service-Scopes: MS,<scope>
traceparent: <traceparent-w3c>
X-Request-Id: <uuid-o-correlacion>
```

**4. Ruta**

Confirmar que `/api/backoffice/parameters/**` está ruteada por el Gateway hacia `tema-12-backoffice-service`.

**5. Entorno de prueba extremo a extremo**

Disponibilidad de un entorno con Gateway + credenciales/token M2M para verificar el GET con token de servicio de punta a punta, y cómo se obtiene el token para la prueba.

---

**Nota:** Backoffice rechaza con `401`/`403` si los headers/scope no coinciden con lo esperado; por eso pedimos confirmar estos valores antes de la integración.