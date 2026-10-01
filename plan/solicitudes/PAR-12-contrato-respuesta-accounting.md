# PAR-12 · Respuesta a T11 (Accounting) — contrato definitivo

> **Fecha:** 01/10/2026 · **Emisor:** Backoffice (Tema 12) · **Para:** T11 Accounting (consumo de PAR-12)
> **Fuente de verdad:** Skill Hub `backoffice-global-parameters-contract` v1 · `backoffice-t08-banco-contract` v1

---

## 1 · Mensaje completo para T11 (listo para enviar)

**Respuesta de Backoffice (Tema 12) — Contrato PAR-12**

### 1.1 Campo definitivo del evento: `paramKey`

- El contrato oficial (Skill Hub `backoffice-global-parameters-contract` §3) define el payload del evento como `paramKey`, `value`, `version`.
- El publicador hoy emite `key` (estado provisional). **Lo alineamos a `paramKey`**: el campo definitivo en el **evento** es `paramKey`.
- En la respuesta **REST** el identificador es **`key`** (contrato §2.2): `GET /api/backoffice/parameters/PAR-12` → `{ "key": "PAR-12", "value": {...}, "version": N }`.

### 1.2 `eventVersion` vs `payload.version`

Confirmado como lo plantean: **`payload.version` versiona el valor de PAR-12** (usar para descartar cambios viejos o repetidos, junto con `eventId`); **`eventVersion`** versiona el formato del mensaje (queda en `1`). El evento **no** incluye `createdAt` ni `updatedAt`.

### 1.3 Alcance: GLOBAL

PAR-12 es un **parámetro global**: un solo valor/versión en el registro. `GLOBAL_CONFIGURATION_CHANGED` y la key Kafka `PAR-12` son correctos para este modelo. Aclaramos la descripción (no es "por cada curso"). Si en el futuro se necesitan vidas configurables **por curso**, eso sería un **recurso aparte** (no un PAR global) — se conversa como contrato nuevo.

### 1.4 Validación: `0 <= initialLives <= maxLives` (enteros)

Adoptamos la regla propuesta por Accounting. Alineamos nuestro validador (`ParameterValueRules`) y el contrato publicado a **`0 <= initialLives <= maxLives`** (enteros). Un valor con `initialLives > maxLives`, negativos o no enteros se rechaza.

### 1.5 Identidad / `X-Service-Id`

El contrato publicado consume PAR-12 con **`X-Service-Id: tema-08-banco`** y scope **`backoffice.parameters.read`**. Si T11 es "Accounting", **confirmar el `X-Service-Id` canónico** que va a enviar el Gateway, para que la autorización (authority/scope) no falle en Backoffice.

### 1.6 Prueba extremo a extremo

Pendiente del entorno (Gateway + token de servicio, infraestructura de T01). Del lado Backoffice cubrimos con un **test de integración** (Testcontainers): `GET /parameters/PAR-12` con headers de servicio devuelve el valor, y el evento del outbox contiene `paramKey/value/version`. El flujo M2M del Gateway se coordina con T01.

---

## 2 · Plan de ajustes (nuestro lado)

| # | Ajuste | Tipo | Responsable (quién lo hizo) | Archivos |
|---|---|---|---|---|
| 1 | Publicador del evento: `key` → **`paramKey`** en el payload | **Código** | **Joaquín** (autor de `GlobalConfigurationChangedPayloadDto`/`ParameterChangeRecorderImpl`, US-01 T4 — su javadoc dice "PROVISIONAL") | `dtos/events/GlobalConfigurationChangedPayloadDto.java` · `services/impl/ParameterChangeRecorderImpl.java` (+ `ParameterChangeRecorderImplTest`) |
| 2 | Validación PAR-12: `1 <= initialLives <= maxLives` → **`0 <= initialLives <= maxLives`** | **Código** | **Mateo** (autor de `ParameterValueRules` en PR #56) | `services/impl/ParameterValueRules.java` (+ `ParameterValueRulesTest`) |
| 3 | Descripción de PAR-12: aclarar **alcance global** (no "por cada curso") | **Documentación** | **Mateo** (seed/descripción en V24 y `PARAMETROS.md`) | `V24__...sql` (solo texto de descripción, no valor) · `plan/PARAMETROS.md` · `docs/Contracts/CONTRATOS.md` |
| 4 | Contrato publicado: payload `paramKey/value/version` + validación `0 <=` documentadas | **Contrato (Skill Hub)** | **Backoffice Tema 12** (Mateo es quien mantiene la alineación) | `backoffice-global-parameters-contract` (propose_revision) · `backoffice-t08-banco-contract` |
| 5 | Test de integración del evento (`paramKey`) y del GET con headers de servicio | **Código (test)** | **Mateo** (o quien coordine con Joaquín por el #1) | IT nuevo en el back (Testcontainers) |
| 6 | Confirmar `X-Service-Id`/scope con T11/T01 | **Coordinación** | **T11 + T01** (respuesta del punto 1.5) | — |

### Notas

- El **ajuste #1** toca código de Joaquín: hay que coordinar con él (regla de "no pisar tareas de otro"). Mateo puede implementarlo con su OK o hacerlo Joaquín.
- Los ajustes **#2 y #3** son 100% de Mateo (propios).
- El **#4** sale por `propose_revision` en Skill Hub (pendiente de admin).
- El **#5** depende de que el #1 esté (para asertar `paramKey`).