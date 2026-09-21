# Respuesta de Confirmación — Tema 12 (Backoffice) → Tema 11 (Social y Notificaciones)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Social y Notificaciones (Tema 11)
> **Fecha:** 2026-09-21
> **Referencia:** su respuesta a nuestra solicitud de contratos (2026-09-21)
> **Propósito:** confirmar punto por punto el acuerdo y dejar **cerrada la convención de mensajería** del Sprint 1.

---

## 1 · Convención de Eventos — CONFIRMADO

| Ítem | Acuerdo | Estado |
|---|---|---|
| **Envelope** | `EventEnvelope<T>{eventId, eventType, eventVersion, timestamp, producer, payload}` — **6 campos exactos** | ✅ |
| **`eventVersion`** | = **1** (rechazamos/emitimos solo `eventVersion = 1`) | ✅ |
| **`timestamp`** | ISO-8601 UTC | ✅ |
| **Headers de Kafka** | Solo **`traceparent`** (W3C) obligatorio. `correlationId`/`actorId`/`role` → **dentro del `payload`** | ✅ Ajustamos nuestros docs y consumidores |
| **`producer`** | **`tema-12-backoffice-service`** (convención `tema-XX-service-name`) | ✅ |
| **Serialización** | `JsonSerializer`/`JsonDeserializer` + `spring.json.trusted.packages` + consumer tipado | ✅ |

## 2 · Registro de Tópicos — CONFIRMADO

| Tópico | Estado T11 | Nuestra acción |
|---|---|---|
| `administration.events` | **ACEPTADO** (3 particiones) | Emitimos ahí |
| `administration.events.DLT` | **AUTO-APROVISIONADO** (T11 crea `{topic}` + `{topic}.DLT`) | Usamos el sufijo **mayúsculas**; no lo pedimos por separado |
| `audit.events` | **CONFLICTO con `identity.audit`** | **Decisión:** confluimos en **`identity.audit`** (evitamos duplicado). Coordinamos con T01 que nuestros eventos de auditoría van a ese tópico |

### Qué tópicos necesitamos y para qué

| Tópico | Dirección | Para qué lo necesitamos (requisito) |
|---|---|---|
| `administration.events` | Emitimos | **Propagación de configuración global** (RF-CFG-04/06): al modificar un parámetro (PAR-XX), notificamos a los consumidores (T03/T05/T07/T08/T10) para que actualicen su caché. No es notificación de usuario. |
| `identity.audit` (antes `audit.events`) | Emitimos | **Auditoría** (RF-AUD-*): el Backoffice registra `ParameterChanged` y T01 persiste la bitácora. No es notificación de usuario. |
| `system.notifications` | Emitimos | **Notificaciones a usuarios/tutores** con respaldo en requisitos: `ACADEMIC_DATA_EXPIRING` (preaviso de retención, RF-NFR-10), `STUDENT_AT_HIGH_RISK` (alerta de alumno en riesgo) y `EXPORT_READY` (exportación asíncrona lista, US-09). |
| `challenges.results` | Consumimos | Ingesta del hecho único de resultado de desafío (T03) para nuestros read models de engagement. |
| `courses.lifecycle` | Consumimos | Matrícula y ciclo de vida de cursos (T02) para mantener cohortes y encuestas CSAT. |
| `economy.transactions` | Consumimos | Saldo de monedas por alumno (T08) para frescura en el panel. |
| `sandbox.events` | Consumimos | Progreso/niveles (T10) para analíticas y alertas de riesgo. |

## 3 · Tópicos que consumimos — CONFIRMADO (mapeo al catálogo oficial)

| Tópico oficial | Uso Backoffice | Estado |
|---|---|---|
| `challenges.results` | T03 · resultado de desafío | ✅ ratificado |
| `courses.lifecycle` | T02 · matrícula/ciclo de vida | ✅ ratificado |
| `economy.transactions` | T08 · saldo (antes `bank.events`) | ✅ |
| `sandbox.events` | T10 · progreso (antes `roadmap.events`) | ✅ |
| `identity.audit` | T01 · identidad/auditoría (antes `identity.events` y `audit.events`) | ✅ |
| `retention.events` | No existe tópico propio → **auditoría de retención por `identity.audit`** | ✅ |

**`group.id`:** usamos el propio **`tema-12-backoffice-group`** (nunca reutilizamos el de otro equipo).

## 4 · Eventos emitidos hacia T11 — CONFIRMADO

| Evento | Tópico | T11 consume en MVP | Confirmación |
|---|---|---|---|
| `ACADEMIC_DATA_EXPIRING` | `system.notifications` | Sí (campana in-app general) | ✅ payload `{cohortId, courseName, closingDate, expirationDate, daysRemaining}` |
| `STUDENT_AT_HIGH_RISK` | `system.notifications` | **Sí** (incluye `studentId` + `courseId`) | ✅ lo emitimos |
| `EXPORT_READY` | `system.notifications` | **Sí** | ✅ lo emitimos |
| `DATA_STALE_DETECTED` | — | **No** (operativa interna) | ❌ **no lo emitimos** a la campana |
| `THRESHOLD_BREACHED` | — | **No** (monitoreo interno) | ❌ **no lo emitimos** a la campana |

---

## 5 · Cierre

Con esto **ratificamos la convención de mensajería del Sprint 1** (G1 ✅). Único pendiente acordado: la **coordinación con T01** para que nuestros eventos de auditoría confluya en `identity.audit` (lo resolvemos del lado de T01, sin crear un stream administrativo separado).

¡Gracias por el registro! Con esto alineamos topics, envelope y payloads antes de implementar la capa de mensajería.