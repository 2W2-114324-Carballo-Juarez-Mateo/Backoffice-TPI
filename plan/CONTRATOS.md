# Registro de Contratos Cross-Team — Backoffice (Tema 12)

> **Fuente única de verdad** del estado de los contratos de integración. Cada contrato indica: tema, relación (consumimos / proveemos), estado y dónde está definido. Las solicitudes viven en `plan/solicitudes/`. Los detalles acordados con **T01** están consolidados abajo; el resto quedan **pendientes**.

## Estado por tema

| Tema | Relación con el Backoffice | Estado | Referencia |
|---|---|---|---|
| **T01 · Usuarios/Identidad** | Consume (auth/roles/auditoría/retención/cuentas) + provee (auditoría) | ✅ **CERRADO** | `solicitudes/CONTRATOS_T01_SOLICITUD.md` |
| **T08 · Banco** | Consume (lectura REST + evento de saldo) · **NO consume PAR** | 🟡 **ACUERDO** | `solicitudes/CONTRATOS_T08_RESPUESTA.md` |
| **T10 · Roadmap y Progreso** | Consume (progreso/XP/niveles) · PAR-21 pendiente de validación | 🟡 **EN CURSO** (solicitud enviada) | `solicitudes/CONTRATOS_T10_SOLICITUD.md` |
| **T09 · Mercado** | Provee (dueño de precios **PAR-06/07**) | 🟡 **SOLICITUD LISTA** | `solicitudes/CONTRATOS_T09_SOLICITUD.md` |
| **T11 · Social y Notificaciones** | Coordina **convención de eventos** + consumimos avisos/alertas | 🟡 **SOLICITUD LISTA** | `solicitudes/CONTRATOS_T11_SOLICITUD.md` |
| **T02 · Cursos y Matrícula** | Consume (cohorte `course_id`, pertenencia docente, `RosterUpdated`, **encuestas CSAT anónimas**) + provee (PAR-18) | 🟡 **SOLICITUD LISTA** | `solicitudes/CONTRATOS_T02_SOLICITUD.md` |
| **T04 · Teóricos y Encuestas** | **Sin lectura** — encuestas ahora de **T02**; no consumimos nada de T04 por el momento | ➖ SIN CONTRATO | — |
| **T05 · Desafíos Prácticos** | Consume (entregas/resultados) + provee (PAR-19/20) | ⏳ PENDIENTE | — |
| **T07 · Evaluación LLM** | Consume (deriva/calibración) + provee (`ModelProviderChanged`, PAR-22) | ⏳ PENDIENTE | — |
| **T03 · Desafíos** | Provee (hecho único en `challenge.events`) + consume PAR (PAR-01/04/05; PAR-03 vía Mercado) | 🟡 **ACUERDO** | `solicitudes/CONTRATOS_T03_RESPUESTA.md` |

> **Pendientes internos:** schema externo de `identity.events` y `retention.events` · confirmación formal del `role` en el envelope (T01) · naming de topics con T11 · exposición del estado 2FA (T01) · **API de Vault (T01)** · **GESTOR / "profesor con vista"** · **lista blanca de profesores** · **observabilidad de microservicios: FUERA de alcance** (solo logs/health/correlation).

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
- `/api/auth/*` y `/api/admin/accounts/*` son de T01. Último ADMIN y baja 2FA validadas 100% en users-service. `identity.events` payloads pendientes.

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
- **Contract 3 (PAR):** **no aplica** → los montos los deriva **T03**; **PAR-03/06/07 los gestiona T09 (Mercado)** (descartados del Backoffice); T03 toma PAR-03 de Mercado; **PAR-21** no está en el PRD (candidato suspendido).
- **PAR-12 (vidas iniciales/máximo) → ahora de Banco:** Banco la gestiona y publica el evento **`PARAMETER_UPDATED`** (`bank.events`, **envelope estándar + `version`**) → **✅ acordado** (payload con envelope en `CONTRATOS_T08_RESPUESTA.md`). Backoffice **no la almacena** (EXTERNO). El **evento de saldo/vidas por alumno** queda **⏳ pendiente de confirmar** (topic + payload).
- **Naming:** el evento de saldo se alinea con la **lista de canales habilitados por release** que publica **T11**.
- **RF-RPT-06:** no es RF del PRD → pasa a "decisión de arquitectura (frescura ≤15 min)".

## T03 — Motor de Desafíos (ACUERDO)

- **Hecho único ✅:** `IntentoDesafioFinalizado` (+ `IntentoDesafioCerrado`) en **`challenge.events`**, con desglose y `parametros_aplicados` (versión). Consumimos para read models de engagement.
- **PAR que consume:** PAR-01, PAR-04, PAR-05 (+ PAR-02 no-MVP, PAR-13 como techo de reintentos). **No usa:** PAR-08/12/22 y el resto.
- **PAR-03 es de Mercado** (descartado del Backoffice): **T03 necesita que Mercado lo exponga** (evento + REST + versión) para el monto de monedas del hecho único — contrato **T03 ↔ Mercado**.
- **Necesita de nosotros:** `GET /api/administration/parameters` con `{key, value, version}` (confirmado) · snapshot por intento · aviso de cambios PAR-13.
- **PAR-20:** en **suspenso** (definir comportamiento de la ventana de gracia) · **PAR-25 (nuevo candidato):** plazo máximo de corrección/vencimiento de intento.
- **Métricas:** por **eventos** (`challenge.events`), no endpoints de agregación. → `solicitudes/CONTRATOS_T03_RESPUESTA.md`

## T09 — Mercado (SOLICITUD LISTA)
- **PAR-03 / PAR-06 / PAR-07:** los gestiona **T09 (Mercado)** (descartados del Backoffice). Pendiente: que **Mercado confirme que gestiona PAR-03 y lo expone a T03** (evento + REST + versión). La solicitud `CONTRATOS_T09_SOLICITUD.md` se ajusta.

## T11 — Social y Notificaciones (SOLICITUD LISTA)
- Define la **convención de eventos** de la plataforma (naming de topics, envelope, versionado) y **publica la lista de canales habilitados por release** (para alinear topics como `bank.events`).
- Backoffice **emite** avisos/alertas hacia T11: `DataStaleDetected`, `DataFreshnessRestored`, `StudentAtHighRisk`, `ThresholdBreached`, `ExportReady`. → `solicitudes/CONTRATOS_T11_SOLICITUD.md`

---

## Pendientes por tema (para avanzar)

| Tema | Qué falta acordar | Solicitud |
|---|---|---|
| **T10** | `roadmap.events` (naming con T11), lecturas `/api/roadmap/**`, promoción/abandono y alumno en riesgo, PAR-21 (pendiente de validación) | `solicitudes/CONTRATOS_T10_SOLICITUD.md` |
| **T02** | `course_id`/cohorte, pertenencia docente, `RosterUpdated`, **encuestas CSAT anónimas**, PAR-18 | `solicitudes/CONTRATOS_T02_SOLICITUD.md` |
| **T05** | Entregas/resultados, topic, PAR-19/20 | a generar |
| **T07** | `ModelProviderChanged` (nosotros→ellos), deriva/calibración/golden set (ellos→nosotros), PAR-22 | a generar |
| **T03** | Consumo de PAR-01 (y otros de XP), lectura de métricas de desafíos | `solicitudes/CONTRATOS_T03_SOLICITUD.md` |

> **T04 · Teóricos/Encuestas:** quedó **sin lectura** — las encuestas ahora son de **T02 (Cursos)**; no hay contrato con T04 por el momento.

## Convenciones transversales (aplican a todos)

- **Envelope estándar:** `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}` (rol propuesto como estándar, T01 lo confirma).
- **Topics:** `{dominio}.events` (naming con T11), versionados. **Idempotencia:** `event_id` + `version`.
- **Rutas:** `/api/{servicio}/**`. **Frescura de lectura:** ≤ 15 min (decisión de arquitectura).
- **Caché de parámetros en consumidores:** TTL 10 min + invalidación por evento.

> **Nota de trazabilidad:** los `RF-RPT-*` (reportes/métricas) son **derivados del equipo**, no del PRD (el PRD no los numera). Se citan como referencia interna; la frescura ≤15 min es decisión de arquitectura.

> Fuentes: doc fuente `backoffice_backend_requerimientos_arquitectura.md` (§4.6/4.7, §9.0, §12, §18, §35.2) · solicitudes en `plan/solicitudes/`.