# PAR-12 · Respuesta a T11 (Accounting) — contrato definitivo

> **Fecha:** 01/10/2026 · **De:** Backoffice (Tema 12) · **Para:** T11 Accounting (consumo de PAR-12)
> **Fuente de verdad:** Skill Hub `backoffice-global-parameters-contract` v1 · `backoffice-t08-banco-contract` v1

---

## 1 · Mensaje para T11 (para enviar)

### Respuesta de Backoffice (Tema 12) — Contrato del parámetro PAR-12

A continuación quedan definidos los puntos del contrato de PAR-12 consultados por Accounting.

**1. Identificador del parámetro**

- **Evento Kafka (`administration.events`):** el campo del payload que identifica el parámetro es **`paramKey`**.

  ```json
  {
    "eventId": "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d",
    "eventType": "GLOBAL_CONFIGURATION_CHANGED",
    "eventVersion": 1,
    "timestamp": "2026-10-01T14:00:00Z",
    "producer": "tema-12-backoffice-service",
    "payload": {
      "paramKey": "PAR-12",
      "value": {
        "initialLives": 5,
        "maxLives": 5
      },
      "version": 2
    }
  }
  ```

- **Endpoint REST:** el identificador del parámetro es **`key`**.

  ```
  GET /api/backoffice/parameters/PAR-12

  HTTP/1.1 200 OK
  {
    "key": "PAR-12",
    "value": {
      "initialLives": 5,
      "maxLives": 5
    },
    "version": 2
  }
  ```

  La respuesta puede incluir campos adicionales (por ejemplo, metadatos); el contrato garantiza `key`, `value` y `version`.

**2. Versionado de eventos**

- `eventVersion`: versiona el **formato del mensaje**. La versión vigente es `1`.
- `payload.version`: versiona el **valor de PAR-12**. Es el campo a utilizar para descartar actualizaciones viejas o repetidas: si `payload.version` es menor o igual a la versión en caché, la actualización se ignora. La deduplicación también puede apoyarse en `eventId`.
- El evento **no** incluye `createdAt` ni `updatedAt`.

**3. Alcance del parámetro**

- PAR-12 se administra como **parámetro global de plataforma**: existe un único valor activo y una única secuencia de versiones, aplicable a todos los cursos.
- En consecuencia, `GLOBAL_CONFIGURATION_CHANGED` y la clave de partición Kafka `PAR-12` son correctos para este modelo.
- La descripción "por cada curso" no refleja el modelo vigente. Si en el futuro se requieren vidas configurables por curso, se definirá un recurso y un contrato separados (no es un parámetro global).

**4. Validación (regla definitiva)**

- `initialLives` y `maxLives` deben ser **enteros** y cumplir **`0 <= initialLives <= maxLives`**.
- Backoffice rechaza valores que no cumplan la regla: no enteros, negativos, o `initialLives > maxLives`.

**5. Consumo vía API Gateway**

- Endpoint: `GET /api/backoffice/parameters/PAR-12`.
- Headers de servicio requeridos:
  - `X-Principal-Type: service`
  - `X-Service-Id: <identificador canónico de Accounting>`
  - `X-Service-Scopes: MS`
  - `traceparent` y `X-Request-Id` (trazabilidad)
- Scope de lectura: `backoffice.parameters.read`.
- El contrato publicado hoy referencia el consumo de PAR-12 con `X-Service-Id: tema-08-banco`. Pedimos **confirmar el identificador canónico** que enviará Accounting, para que la autorización coincida en Backoffice.

**6. Verificación extremo a extremo**

- Backoffice verificará el endpoint (GET con headers de servicio) y el evento publicado (envelope con `paramKey`) mediante tests de integración.
- La verificación completa contra el entorno real (Gateway + credenciales de servicio) depende de la infraestructura del Gateway; coordinaremos su ejecución cuando esté disponible.

---

## 2 · Plan de ajustes — INTERNO (no se envía)

| # | Ajuste | Tipo | Responsable (quién lo hizo) | Archivos |
|---|---|---|---|---|
| 1 | Publicador del evento: `key` → **`paramKey`** en el payload | Código | **Joaquín** (autor de `GlobalConfigurationChangedPayloadDto`/`ParameterChangeRecorderImpl`, US-01 T4 — su javadoc dice "PROVISIONAL") | `dtos/events/GlobalConfigurationChangedPayloadDto.java` · `services/impl/ParameterChangeRecorderImpl.java` (+ test) |
| 2 | Validación PAR-12: `1 <= initialLives <= maxLives` → **`0 <= initialLives <= maxLives`** | Código | **Mateo** (autor de `ParameterValueRules`, PR #56) | `services/impl/ParameterValueRules.java` (+ test) |
| 3 | Descripción de PAR-12: aclarar **alcance global** (no "por cada curso") | Documentación | **Mateo** (seed/descripción en V24 y `PARAMETROS.md`) | `V24__...sql` (solo texto de descripción) · `plan/PARAMETROS.md` · `docs/Contracts/CONTRATOS.md` |
| 4 | Contrato publicado: payload `paramKey/value/version` + validación `0 <=` documentadas | Contrato (Skill Hub) | **Backoffice Tema 12** (Mateo mantiene la alineación) | `backoffice-global-parameters-contract` (propose_revision) · `backoffice-t08-banco-contract` |
| 5 | Test de integración del evento (`paramKey`) y del GET con headers de servicio | Código (test) | **Mateo** (con OK de Joaquín por el #1) | IT nuevo en el back (Testcontainers) |
| 6 | Confirmar `X-Service-Id`/scope con T11/T01 | Coordinación | **T11 + T01** (respuesta del punto 5 del mensaje) | — |

**Notas:**

- El **#1** toca código de Joaquín (regla de no pisar tareas ajenas): coordinarlo antes de implementar.
- Los **#2 y #3** son 100% de Mateo.
- El **#4** sale por `propose_revision` en Skill Hub (pendiente de admin).
- El **#5** depende de que el #1 esté (para asertar `paramKey`).