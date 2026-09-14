# Registro de Contratos Cross-Team — Backoffice (Tema 12)

> **Fuente única de verdad** del estado de los contratos de integración. Cada contrato indica: tema, relación (consumimos / proveemos), estado y dónde está definido. Las solicitudes viven en `plan/solicitudes/`. Los detalles acordados con **T01** están consolidados abajo; el resto quedan **pendientes**.

## Estado por tema

| Tema | Relación con el Backoffice | Estado | Referencia |
|---|---|---|---|
| **T01 · Usuarios/Identidad** | Consume (auth/roles/auditoría/retención/cuentas) + provee (auditoría) | ✅ **CERRADO** | `solicitudes/CONTRATOS_T01_SOLICITUD.md` |
| **T08 · Banco** | Consume (lectura REST: saldos/movimientos) · **NO consume PAR** | 🟡 **ACUERDO PARCIAL** (REST-only) | `solicitudes/CONTRATOS_T08_SOLICITUD.md` |
| **T10 · Roadmap y Progreso** | Consume (progreso/XP/niveles) · PAR-21 pendiente de validación | 🟡 **EN CURSO** (solicitud enviada) | `solicitudes/CONTRATOS_T10_SOLICITUD.md` |
| **T09 · Mercado** | Provee (dueño de precios **PAR-06/07**) | 🟡 **SOLICITUD LISTA** | `solicitudes/CONTRATOS_T09_SOLICITUD.md` |
| **T11 · Social y Notificaciones** | Coordina **convención de eventos** + consumimos avisos/alertas | 🟡 **SOLICITUD LISTA** | `solicitudes/CONTRATOS_T11_SOLICITUD.md` |
| **T02 · Cursos y Matrícula** | Consume (cohorte `course_id`, pertenencia docente, `RosterUpdated`) | ⏳ PENDIENTE | — |
| **T04 · Teóricos y Encuestas** | Consume (agregados anónimos CSAT) | ⏳ PENDIENTE | — |
| **T05 · Desafíos Prácticos** | Consume (entregas/resultados) + provee (PAR-19/20) | ⏳ PENDIENTE | — |
| **T07 · Evaluación LLM** | Consume (deriva/calibración) + provee (`ModelProviderChanged`, PAR-22) | ⏳ PENDIENTE | — |
| **T03 · Desafíos** | Provee (PAR-01/03: deriva montos) + lectura de métricas | ⏳ PENDIENTE | — |

> **Pendientes internos:** schema externo de `identity.events` y `retention.events` · confirmación formal del `role` en el envelope (T01) · naming de topics con T11 · exposición del estado 2FA (T01).

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

---

## T08 — Banco (ACUERDO PARCIAL)

- **Lectura:** REST-only (`/api/bank/**` — balances, balance por alumno, transactions, por `courseId`). Sin `bank.events` en el MVP.
- **`distribution`:** a Banco solo **saldo en monedas**; la **distribución de XP** la expone **T10**.
- **"Retención vs desafíos":** lo **integra el Backoffice** (cruce T03/05 + Banco); no se pide a Banco.
- **Contract 3 (PAR):** **no aplica** → PAR-01/03 los deriva **T03**; PAR-06/07 los decide **T09 (Mercado)**; **PAR-21** no está en el PRD (candidato suspendido, depende de rachas/T10).
- **RF-RPT-06:** no es RF del PRD → pasa a "decisión de arquitectura (frescura ≤15 min)".

## T09 — Mercado (SOLICITUD LISTA)
- Consume **PAR-06** (precio vida) y **PAR-07** (precio equipamiento) para armar el catálogo; mecanismo `GlobalConfigurationChanged` + caché TTL 10 min. → `solicitudes/CONTRATOS_T09_SOLICITUD.md`

## T11 — Social y Notificaciones (SOLICITUD LISTA)
- Define la **convención de eventos** de la plataforma (naming de topics, envelope, versionado).
- Backoffice **emite** avisos/alertas hacia T11: `DataStaleDetected`, `DataFreshnessRestored`, `StudentAtHighRisk`, `ThresholdBreached`, `ExportReady`. → `solicitudes/CONTRATOS_T11_SOLICITUD.md`

---

## Pendientes por tema (para avanzar)

| Tema | Qué falta acordar | Solicitud |
|---|---|---|
| **T10** | `roadmap.events` (naming con T11), lecturas `/api/roadmap/**`, promoción/abandono y alumno en riesgo, PAR-21 (pendiente de validación) | `solicitudes/CONTRATOS_T10_SOLICITUD.md` |
| **T02** | `course_id`/cohorte, contrato de pertenencia docente, `RosterUpdated`, lectura de matrícula | a generar |
| **T04** | Agregados anónimos de encuestas (CSAT), topic `survey.events` | a generar |
| **T05** | Entregas/resultados, topic, PAR-19/20 | a generar |
| **T07** | `ModelProviderChanged` (nosotros→ellos), deriva/calibración/golden set (ellos→nosotros), PAR-22 | a generar |
| **T03** | Consumo de PAR-01/03, lectura de métricas de desafíos | a generar |

## Convenciones transversales (aplican a todos)

- **Envelope estándar:** `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}` (rol propuesto como estándar, T01 lo confirma).
- **Topics:** `{dominio}.events` (naming con T11), versionados. **Idempotencia:** `event_id` + `version`.
- **Rutas:** `/api/{servicio}/**`. **Frescura de lectura:** ≤ 15 min (decisión de arquitectura).
- **Caché de parámetros en consumidores:** TTL 10 min + invalidación por evento.

> **Nota de trazabilidad:** los `RF-RPT-*` (reportes/métricas) son **derivados del equipo**, no del PRD (el PRD no los numera). Se citan como referencia interna; la frescura ≤15 min es decisión de arquitectura.

> Fuentes: doc fuente `backoffice_backend_requerimientos_arquitectura.md` (§4.6/4.7, §9.0, §12, §18, §35.2) · solicitudes en `plan/solicitudes/`.