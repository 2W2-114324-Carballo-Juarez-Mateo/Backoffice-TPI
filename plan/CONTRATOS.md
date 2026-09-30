# Registro de Contratos Cross-Team — Backoffice (Tema 12)

> **Fuente única de verdad** del estado de los contratos de integración. Cada contrato indica: tema, relación (consumimos / proveemos), estado y dónde está definido. Las solicitudes viven en `plan/solicitudes/`. Los detalles acordados con **T01** están consolidados abajo; el resto quedan **pendientes**.
>
> **✅ Estándar de eventos CERRADO (T11/cátedra, 2026-09):** **`EventEnvelope<T>{eventId (UUID), eventType, eventVersion (int), timestamp, producer, payload (T)}`** (6 campos; `correlationId/actorId/role` → **dentro del `payload`**). **Todo en inglés.** `producer` = `spring.application.name` → **`tema-12-backoffice-service`**. **No se crean topics nuevos: se registran con T11.** Backoffice emite `ACADEMIC_DATA_EXPIRING` → `system.notifications`. Queda coordinar con cada tema: **registrar nuestros topics** (config/auditoría/DLT) con T11 (G1), payloads por evento y confirmación del producer.

## Estado por tema

| Tema | Relación con el Backoffice | Estado | Referencia |
|---|---|---|---|
| **T01 · Usuarios/Identidad** | Consume (auth/roles/auditoría/retención/cuentas) + provee (auditoría) | ✅ **CERRADO** | `solicitudes/CONTRATOS_T01_SOLICITUD.md` |
| **T08 · Banco** | Consume (lectura REST + evento de saldo) · **NO consume PAR** | 🟡 **ACUERDO** | `solicitudes/CONTRATOS_T08_RESPUESTA.md` |
| **T10 · Roadmap y Progreso** | Consume (progreso/XP/niveles) · PAR-21 pendiente de validación | 🟡 **EN CURSO** (solicitud enviada) | `solicitudes/CONTRATOS_T10_SOLICITUD.md` |
| **T09 · Mercado** | Consume de nosotros **PAR-03, PAR-06, PAR-07** (precios/catálogo) | 🟡 **SOLICITUD LISTA** | `solicitudes/CONTRATOS_T09_SOLICITUD.md` |
| **T11 · Social y Notificaciones** | Coordina **convención de eventos** + consumimos avisos/alertas | ✅ **CERRADO (G1 + formal 27/09)** | `solicitudes/CONTRATOS_T11_SOLICITUD.md` · `solicitudes/CONTRATOS_T11_RESPUESTA.md` · `respuesta-t12.md` · `solicitudes/analisis-brechas-t12.md` |
| **T02 · Cursos y Matrícula** | Consume (cohorte `course_id`, pertenencia docente, `RosterUpdated`, **encuestas CSAT anónimas**) + provee (PAR-18) | 🟡 **SOLICITUD LISTA** | `solicitudes/CONTRATOS_T02_SOLICITUD.md` |
| **T04 · Teóricos y Encuestas** | **Sin lectura** — encuestas ahora de **T02**; no consumimos nada de T04 por el momento | ➖ SIN CONTRATO | — |
| **T05 · Desafíos Prácticos** | Consume (entregas/resultados) + provee (PAR-19/20) | ⏳ PENDIENTE | — |
| **T07 · Evaluación LLM** | **Fachada de gobernanza:** consumimos `/api/llm/admin/*` de `llm-service` v2.0.0 (proveedores, modelos, activación, uso) · provee (`ModelProviderChanged`, PAR-22) + recibe **alerta de presupuesto** (70%→Backoffice) | ✅ **CERRADO (fachada, 27/09)** — dominio LLM de T07; Backoffice es fachada, sin tablas ni lógica LLM propias | Skill Hub `llm-service-http-contract` v6 · `docs/Task/auditoria-contratos-skillhub.md` |
| **T03 · Desafíos** | Provee (hecho único en `challenge.events`) + consume PAR (PAR-01/04/05; PAR-03 de nuestro registro) | 🟡 **ACUERDO** | `solicitudes/CONTRATOS_T03_RESPUESTA.md` |

> **Pendientes internos:** schema externo de `identity.audit` y `retention.events` (confluir nuestra auditoría en `identity.audit`, coordinar con **T01**) · materializar los topics del Backoffice en el broker (T11 los tiene **uncommitted** en `fix/contract-alignment`) · exposición del estado 2FA (T01) · **API de Vault (T01)** · **GESTOR / "profesor con vista"** · **lista blanca de profesores** · **observabilidad de microservicios: FUERA de alcance** (solo logs/health/correlation).

---

## T01 — Contratos CERRADOS (resumen de lo acordado)

### 1 · Autenticación / JWT
- Claims: `sub`, `roles[]`, `type:"user"`, `jti`, `sid`, `est`, `pwd`, `onb`, `iat`, `exp`. RS256. JWKS `/.well-known/jwks.json`. Access ~10 min; refresh ~7 días (front). **El gateway valida; el Backoffice nunca valida firma/exp.** No hay `permissions[]` ni `courseScope`.

### 2 · Contexto por gateway (headers)
- `X-Principal-Type`, `X-User-Id`, `X-Service-Id`, `X-User-Roles`, `X-Service-Scopes`, `traceparent` (W3C), `X-Request-Id`. Anti-spoofing. **No existe `X-Course-Id`**: el alcance se deriva server-side.

### 3 · Roles y autorización
- Roles reales: `ADMIN`, `PROFESOR`, `ALUMNO` + `MS` (solo service-to-service). **No existe `AUDITOR`**. Autorización **local** con `@PreAuthorize` (no hay endpoint REST de autorización).

### 4 · Auditoría
- Publicación: topic `audit.events` (v1); eventos del Backoffice: `ParameterChanged`, `ModelProviderChanged`, `EvaluatorActivated`, `GlobalRead`; envelope + `role`.
- Lectura: `GET /api/users/audit` y `GET /api/users/audit/{id}` (ADMIN, paginado `{items,total,nextOffset}`).

### 5 · Retención
- Backoffice **no purga por su cuenta**; alinea read models ante `DataAnonymized`/`RetentionDecisionCreated`. Lectura opcional postergada: `GET /api/users/retention/*`.

### 6 · Gestión de cuentas ADMIN
- `/api/auth/*` y `/api/admin/accounts/*` son de T01. Último ADMIN y baja 2FA validadas 100% en users-service. `identity.audit` payloads pendientes.
- **Contrato cuentas confirmado (27/09, T01):** **no existe `/api/admin/accounts`**; la API real es **`/api/users`**: `GET /api/users` → array directo sin `remainingAdmins` (solo activas; GESTOR ve PROFESSOR/GESTOR) · `PATCH /api/users/{id}/role` con body `{role}` · `POST /api/users` (solo ADMIN, crea solo ADMIN) · `DELETE /api/users/{id}` con body `{password, twoFactorCode, usernameConfirmation}` (usernameConfirmation = **email**). Errores `problem+json` con `type` = URI completa (`last-admin` 409, `access-denied` 403, `duplicate-email`, `invalid-code`, `invalid-credentials`). Evidencia: `MENSAJE-T01-CUENTAS-ADMIN.md`.

### 7 · 2FA
- Hoy OTP por email; evaluando TOTP. Exposición del estado sin definir → front usa mock.

### 8 · PAR-24 y trazabilidad
- PAR-24 → ownership **T01**. Trazabilidad: `traceparent` + `X-Request-Id`.

### 9 · Convención de rutas
- Toda API bajo `/api/{servicio}/**`. Backoffice: `/api/administration/**` y `/api/reports/**`. T01: `/api/users/**`.

### 10 · Vault (secretos) — NUEVO, a coordinar con T01
- **T01 implementa y administra el Vault.** El Backoffice **solo consume** el servicio para **enviar los secretos** (API Keys LLM) que T01 almacene; en nuestra BD guardamos una **referencia enmascarada** (path/id), no el secreto.
- **Pendiente:** API de envío (write), refs, y rotación (¿la hace T01?).

### 11 · GESTOR y "PROFESOR con permiso de vista" — a coordinar con T01
- Los roles los define **T01** (confirmó ADMIN/PROFESOR/ALUMNO + MS; **no existe GESTOR**). El profe plantea niveles **GESTOR** y **"PROFESOR con permiso de vista"** → **coordinar** si son **roles nuevos** (T01) o **permisos/scopes** que modelamos nosotros en Backoffice.
- Mientras tanto, el Backoffice define la **matriz de acciones por rol** (qué puede hacer cada uno en el panel) con los roles base.

### 12 · Lista blanca de profesores (RF-USR-02) — a coordinar con T01
- Dueño: **T01** (alta de PROFESOR por whitelist). Posible: Backoffice la **administra desde el panel** consumiendo la API de T01. **Pregunta a T01:** ¿la gestionan ellos o la administramos nosotros?

---

## T08 — Banco (ACUERDO PARCIAL)

- **Lectura:** **REST (`/api/bank/**`) para replay/inicial + nos suscribimos al evento de actualización de saldo por alumno/curso** (decisión tomada; REST ya no es solo polling — el evento mejora recursos y frescura). → `solicitudes/CONTRATOS_T08_RESPUESTA.md`
- **`distribution`:** a Banco solo **saldo en monedas**; la **distribución de XP** la expone **T10**.
- **"Retención vs desafíos":** **retención de monedas → Banco**, **retención de XP → Roadmap (T10)**; el **Backoffice integra ambos** en el agregado (no se pide a Banco).
- **Contract 3 (PAR):** los montos los deriva **T03**; **PAR-03/06/07 son del Backoffice** y los consumen **T09 (Mercado)** y **T03 (PAR-03)** desde nuestro registro (Skill Hub); **PAR-21** no está en el PRD (candidato suspendido).
- **PAR-12 (vidas iniciales/máximo) — del Backoffice (lo consume T08):** **PAR-12 es administrado por Backoffice** (Skill Hub `backoffice-t08-banco-contract` v1) y lo consume **T08 (Banco)**. Banco **no lo gestiona** ni publica `PARAMETER_UPDATED`; las modificaciones salen por `administration.events`. El **evento de saldo/vidas por alumno** de Banco queda **⏳ pendiente de confirmar** (topic + payload).
- **Naming:** el evento de saldo se alinea con la **lista de canales habilitados por release** que publica **T11**.
- **RF-RPT-06:** no es RF del PRD → pasa a "decisión de arquitectura (frescura ≤15 min)".

## T03 — Motor de Desafíos (ACUERDO)

- **Hecho único ✅:** `IntentoDesafioFinalizado` (+ `IntentoDesafioCerrado`) en **`challenge.events`**, con desglose y `parametros_aplicados` (versión). Consumimos para read models de engagement.
- **PAR que consume:** PAR-01, PAR-04, PAR-05 (+ PAR-02 no-MVP, PAR-13 como techo de reintentos). **No usa:** PAR-08/12/22 y el resto.
- **PAR-03 es del Backoffice** (Skill Hub): **T03 y T09 lo consumen de nuestro registro** para el monto de monedas del hecho único; no hay contrato T03 ↔ Mercado por PARs.
- **Necesita de nosotros:** `GET /api/administration/parameters` con `{key, value, version}` (confirmado) · snapshot por intento · aviso de cambios PAR-13.
- **PAR-20:** en **suspenso** (definir comportamiento de la ventana de gracia) · **PAR-25 (nuevo candidato):** plazo máximo de corrección/vencimiento de intento.
- **Métricas:** por **eventos** (`challenge.events`), no endpoints de agregación. → `solicitudes/CONTRATOS_T03_RESPUESTA.md`

## T09 — Mercado (SOLICITUD LISTA)
- **PAR-03 / PAR-06 / PAR-07:** son del **Backoffice**; **T09 (Mercado) los consume** de nuestro registro (Skill Hub). Pendiente: que **Mercado confirme que los lee del registro** (no los hardcodea). La solicitud `CONTRATOS_T09_SOLICITUD.md` se ajusta.

## T11 — Social y Notificaciones (✅ CERRADO — G1, análisis de brechas 2026-09-21)
- **Convención ratificada:** `EventEnvelope<T>` 6 campos, `eventVersion = 1`, solo `traceparent` como header obligatorio (**`correlationId`/`actorId`/`role` → dentro del `payload`**), `producer = tema-12-backoffice-service`, `JsonSerializer`/`JsonDeserializer` + consumer tipado. **T11 confirma que T12 puede emitir hoy.**
- **Topics emitidos:** `administration.events` ✅ (3 particiones) · DLT = **`administration.events.DLT`** (mayúsculas, auto-aprovisionado) · **auditoría → `identity.audit`** (sin stream separado; coordinar con T01).
- **Topics consumidos (catálogo oficial v3, aplicados en código — PR #47):** `challenges.events` (T03) · `courses.events` (T02) · `accounting.events` (T08) · `identity.audit.events` (T01) · `notifications.events` (T11). **`group.id` = `tema-12-backoffice-group`**.
- **Emisiones a `notifications.events`:** `ACADEMIC_DATA_EXPIRING` ✅ · `STUDENT_AT_HIGH_RISK` ✅ (Sprint 2, US-12) · `EXPORT_READY` ✅ · `DATA_STALE_DETECTED`/`THRESHOLD_BREACHED` ❌ (no, operativas internas).
- **Confirmación formal (27/09):** T11 respondió por escrito en `respuesta-t12.md` ratificando el catálogo de topics, el envelope de 6 campos, el producer `tema-12-backoffice-service` y el provisioning en el mesh (`event-bus/init-topics.sh`, 3 particiones). Referencias: `solicitudes/CONTRATOS_T11_RESPUESTA.md` · `solicitudes/analisis-brechas-t12.md` · `respuesta-t12.md`.
- **Pendiente (coordinación):** confluir la auditoría en **`identity.audit`** del lado de **T01**.

---

## Estándar de eventos de plataforma (PDF de T11) — ✅ CERRADO

| Ítem | Valor oficial |
|---|---|
| Envelope | **`EventEnvelope<T>{eventId, eventType, eventVersion, timestamp, producer, payload}`** (6 campos, todos obligatorios; **nada más en el body**) — genérico con payload tipado |
| `eventVersion` | `int` — **versión del contrato del evento** (evolución del schema; no es la versión de negocio del payload) |
| `correlationId/actorId/role` | **Dentro del `payload`** (trazabilidad/auditoría; a confirmar formalmente con T01/T11) |
| Idioma | **Todo en inglés** (literal del PDF de T11) |
| `eventType` | Inglés SCREAMING_SNAKE_CASE (`CHALLENGE_COMPLETED`, `ACADEMIC_DATA_EXPIRING`…) |
| `producer` | `spring.application.name` (Backoffice: **`tema-12-backoffice-service`**) |
| `timestamp` | ISO 8601 UTC |
| Topics | Inglés (v3, T11): `challenges.events` · `courses.events` · `notifications.events` · `accounting.events` · `identity.audit.events`… |
| Serialización | `JsonSerializer`/`JsonDeserializer` + `spring.json.trusted.packages` + consumer tipado `Event<Payload>` |
| Regla de topics | **No se crean topics nuevos: se avisa a T11 para registrarlos** |

> **Impacto en Backoffice (v3, T11):** consumimos **`challenges.events`** (T03) y **`courses.events`** (T02); también `accounting.events` (T08) y `identity.audit.events` (T01, auditoría delegada). **Emisión:** `administration.events` (config) + `administration.events.DLT` + `notifications.events` (alertas). Nombres de eventos propios (`GLOBAL_CONFIGURATION_CHANGED`, `STUDENT_AT_HIGH_RISK`, `EXPORT_READY`) en inglés. `producer = tema-12-backoffice-service`.

## T07 — Evaluación LLM (✅ CERRADO — fachada de gobernanza, 27/09)
- **Pivote (opción A):** el dominio LLM (proveedores, credenciales, modelos, activación, golden set, calibración PAR-14) es de **T07** (`llm-service` v2.0.0, publicado en Skill Hub). El Backoffice es **fachada de gobernanza**: consume `/api/llm/admin/*` con scope **`llm.calibration.manage`** (security `serviceJwt`) y path var **`{deploymentId}`** (`GET /admin/evaluator-models`, `/active`, `POST .../{deploymentId}/activate`, `.../select-for-calibration`, `.../usage`, `DELETE .../{deploymentId}`). **Sin tablas ni lógica LLM propias** (`V10` eliminado del shared). Fuente: `docs/Task/auditoria-contratos-skillhub.md`.
- **Scope de lectura de parámetros:** aceptado `backoffice.parameters.read` como authority pelada para M2M (PR #51, mergeado). `READ_PARAMS` se mantiene mientras migran.
- **Alerta de presupuesto al 70% → Backoffice:** evento `LLMBudgetAlert` propuesto en `llm.budget.events` — **Sprint 2** (se integra al retomar US-04/US-07).
- **Pendiente de aclarar (T07):** (a) los límites de las 4 capas son **solo visualización** desde Backoffice, o (b) **configurables** (→ parámetros EXTERNOS T07). Recomendamos (a).
- **PAR-22:** valor oficial **3 desafíos/día** (Skill Hub `backoffice-t07-evaluacion-llm-contract` v1) — **es nuestro**, no EXTERNO T07.
- **Golden set/calibración:** **BLOQUEADO** (US-06) — falta cerrar quién ejecuta el golden set y el endpoint de calibración de T07. `solicitudes/CONTRATOS_T07_SOLICITUD.md` · `solicitudes/CONTRATOS_T07_LLM_LIMITES.md`.

---

## Pendientes por tema (para avanzar)

| Tema | Qué falta acordar | Solicitud |
|---|---|---|
| **T10** | `sandbox.events` (naming con T11), lecturas `/api/roadmap/**`, promoción/abandono y alumno en riesgo, PAR-21 (pendiente de validación) | `solicitudes/CONTRATOS_T10_SOLICITUD.md` |
| **T02** | `course_id`/cohorte, pertenencia docente, `RosterUpdated`, **encuestas CSAT anónimas**, PAR-18 | `solicitudes/CONTRATOS_T02_SOLICITUD.md` |
| **T05** | Entregas/resultados, topic, PAR-19/20 | a generar |
| **T07** | **Alerta de presupuesto (70%→Backoffice): topic + payload** · ¿límites configurables o fijos? · PAR-22 (diario 3 vs semanal 5) · deriva/calibración/golden set · PAR-22 | `solicitudes/CONTRATOS_T07_SOLICITUD.md` |
| **T03** | Consumo de PAR-01 (y otros de XP), lectura de métricas de desafíos | `solicitudes/CONTRATOS_T03_SOLICITUD.md` |

> **T04 · Teóricos/Encuestas:** quedó **sin lectura** — las encuestas ahora son de **T02 (Cursos)**; no hay contrato con T04 por el momento.

## Convenciones transversales (aplican a todos)

- **Envelope estándar (T11/cátedra):** **`EventEnvelope<T>{eventId (UUID), eventType, eventVersion (int), timestamp, producer, payload (T)}`** (genérico, payload tipado). `correlationId/actorId/role` → **dentro del `payload`**. **Todo en inglés**. `producer = spring.application.name` (**`tema-12-backoffice-service`**).
- **Topics (v3, T11):** en inglés (`challenges.events`, `courses.events`, `notifications.events`, `accounting.events`…), versionados. **No se crean topics nuevos: se registran con T11.** **Idempotencia:** `event_id` + versión.
- **Rutas:** `/api/{servicio}/**`. **Frescura de lectura:** ≤ 15 min (decisión de arquitectura).
- **Caché de parámetros en consumidores:** TTL 10 min + invalidación por evento.

> **Nota de trazabilidad:** los `RF-RPT-*` (reportes/métricas) son **derivados del equipo**, no del PRD (el PRD no los numera). Se citan como referencia interna; la frescura ≤15 min es decisión de arquitectura.

> Fuentes: doc fuente `backoffice_backend_requerimientos_arquitectura.md` (§4.6/4.7, §9.0, §12, §18, §35.2) · solicitudes en `plan/solicitudes/`.