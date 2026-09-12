# Registro de Contratos Cross-Team — Backoffice (Tema 12)

> **Fuente única de verdad** del estado de los contratos de integración. Cada contrato indica: tema, relación (consumimos / proveemos), estado y dónde está definido. Los detalles acordados con **T01** están consolidados abajo; el resto quedan **pendientes** (con su solicitud generada).

## Estado por tema

| Tema | Relación con el Backoffice | Estado | Referencia |
|---|---|---|---|
| **T01 · Usuarios/Identidad** | Consume (auth/roles/auditoría/retención/cuentas) + provee (auditoría) | ✅ **CERRADO** | `CONTRATOS_T01_SOLICITUD.md` |
| **T10 · Roadmap y Progreso** | Consume (progreso/XP/niveles) + provee (PAR-21) | 🟡 **EN CURSO** (solicitud enviada) | `CONTRATOS_T10_SOLICITUD.md` |
| **T02 · Cursos y Matrícula** | Consume (cohorte `course_id`, pertenencia docente, `RosterUpdated`) | ⏳ PENDIENTE | — |
| **T04 · Teóricos y Encuestas** | Consume (agregados anónimos CSAT) | ⏳ PENDIENTE | — |
| **T05 · Desafíos Prácticos** | Consume (entregas/resultados) + provee (PAR-19/20) | ⏳ PENDIENTE | — |
| **T07 · Evaluación LLM** | Consume (deriva/calibración) + provee (`ModelProviderChanged`, PAR-22) | ⏳ PENDIENTE | — |
| **T08 · Banco** | Consume (XP/monedas) + provee (PAR-21) | ⏳ PENDIENTE | — |
| **T03 · Desafíos** | Provee (PAR-20 y economía) + lectura de métricas | ⏳ PENDIENTE | — |

> Pendientes internos (T01 lo confirmó): schema externo de `identity.events` y `retention.events`, confirmación formal del `role` en el envelope estándar, y la exposición del estado 2FA (claim vs endpoint).

---

## T01 — Contratos CERRADOS (resumen de lo acordado)

### 1 · Autenticación / JWT
- Claims: `sub`, `roles[]`, `type:"user"`, `jti`, `sid`, `est`, `pwd`, `onb`, `iat`, `exp`. RS256. JWKS `/.well-known/jwks.json`. Access ~10 min; refresh ~7 días (front). **El gateway valida; el Backoffice nunca valida firma/exp.** No hay `permissions[]` ni `courseScope`.

### 2 · Contexto por gateway (headers)
- `X-Principal-Type`, `X-User-Id`, `X-Service-Id`, `X-User-Roles`, `X-Service-Scopes`, `traceparent` (W3C), `X-Request-Id`. Anti-spoofing (el gateway limpia y reinyecta). **No existe `X-Course-Id`**: el alcance se deriva server-side.

### 3 · Roles y autorización
- Roles reales: `ADMIN`, `PROFESOR`, `ALUMNO` + `MS` (solo service-to-service). **No existe `AUDITOR`** (lectura de auditoría = `ADMIN`). Claim `roles` (array). Autorización **local** con `@PreAuthorize` sobre el rol propagado (no hay endpoint REST de autorización).

### 4 · Auditoría
- Publicación: topic `audit.events` (v1); eventos del Backoffice: `ParameterChanged`, `ModelProviderChanged`, `EvaluatorActivated`, `GlobalRead`; envelope estándar + `role`.
- Lectura: `GET /api/users/audit` y `GET /api/users/audit/{id}` (ADMIN, `offset`/`limit` def. 50/máx 100, respuesta `{items,total,nextOffset}`).

### 5 · Retención
- Backoffice **no purga por su cuenta**; alinea read models ante `DataAnonymized`/`RetentionDecisionCreated` (payload `entityType`/`entityId` con UUIDs de plataforma). Lectura opcional postergada: `GET /api/users/retention/*`.

### 6 · Gestión de cuentas ADMIN
- `/api/auth/*` y `/api/admin/accounts/*` son de T01. **Último ADMIN** y **baja 2FA** validadas 100% en users-service. `identity.events` payloads pendientes.

### 7 · 2FA
- Hoy OTP por email (6 dígitos); evaluando TOTP. Exposición del estado (claim vs endpoint) **sin definir** → el front usa mock.

### 8 · PAR-24 y trazabilidad
- PAR-24 (session timeout) → ownership **T01**. Trazabilidad: `traceparent` + `X-Request-Id`.

### 9 · Convención de rutas
- Toda API pública bajo `/api/{servicio}/**` (sin prefijo → 404). Backoffice: `/api/administration/**` y `/api/reports/**` (export/alertas bajo `/api/reports/**`). T01: `/api/users/**`.

---

## Pendientes por tema (para avanzar)

| Tema | Qué falta acordar | Solicitud |
|---|---|---|
| **T10** | `roadmap.events` (topic/eventos/payload), lecturas `/api/roadmap/**`, definición promoción/abandono y alumno en riesgo, frescura, PAR-21 | `CONTRATOS_T10_SOLICITUD.md` |
| **T02** | `course_id`/cohorte, contrato de pertenencia docente, `RosterUpdated` (evento), lectura de matrícula | a generar |
| **T04** | Agregados anónimos de encuestas (CSAT), topic `survey.events` | a generar |
| **T05** | Entregas/resultados, topic, PAR-19/20 | a generar |
| **T07** | `ModelProviderChanged` (nosotros→ellos), deriva/calibración/golden set (ellos→nosotros), PAR-22 | a generar |
| **T08** | Saldos/XP/monedas, topic, PAR-21 | a generar |
| **T03** | Consumo de PAR (economía), lectura de métricas de desafíos | a generar |

## Convenciones transversales (aplican a todos)

- **Envelope estándar:** `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}` (rol propuesto como estándar, T01 lo confirma).
- **Topics:** `{dominio}.events`, versionados. **Idempotencia:** `event_id` + `version`.
- **Rutas:** `/api/{servicio}/**`. **Frescura de lectura:** ≤ 15 min.
- **Caché de parámetros en consumidores:** TTL 10 min + invalidación por evento.

> Fuentes: doc fuente `backoffice_backend_requerimientos_arquitectura.md` (§4.6/4.7, §9.0, §12, §18, §35.2) · solicitudes `CONTRATOS_T01_SOLICITUD.md` y `CONTRATOS_T10_SOLICITUD.md`.