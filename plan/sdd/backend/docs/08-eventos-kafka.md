# 08 — Eventos con Kafka

Broker elegido: **Kafka** (ADR-003). **RabbitMQ** queda como alternativa. Mismos patrones de confiabilidad: **Outbox + at-least-once + idempotencia**.

## Envelope común — `EventEnvelope<T>` (T11/cátedra, ✅ CERRADO)

```json
{
  "eventId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "eventType": "EVENT_NAME_IN_ENGLISH",
  "eventVersion": 1,
  "timestamp": "2026-09-02T19:30:00Z",
  "producer": "backoffice-service",
  "payload": {}
}
```

- **6 campos** (todos obligatorios) y nada más en el body; es el genérico **`EventEnvelope<T>`** con **payload tipado** (`eventId` UUID · `eventType` · `eventVersion` int · `timestamp` · `producer` · `payload`). **Todo en inglés.** `producer` = `spring.application.name` (**`backoffice-service`**).
- **`eventVersion`**: versión del **contrato del evento** (evolución del schema). No es la versión de negocio del payload.
- **Serialización (T11):** `JsonSerializer`/`JsonDeserializer` + `spring.json.trusted.packages` + consumer tipado `EventEnvelope<Payload>`.
- **`correlationId` / `actorId` / `role` → headers de Kafka** (trazabilidad y auditoría; a confirmar formalmente con T01/T11). No viajan en el body.
- **Regla de topics:** **no se crean topics nuevos — se registran con T11 (G1).** Topics a registrar: `administration.events` (config), `audit.events`, `administration.events.dlt` (DLT).
- Backoffice emite a **`system.notifications`**: **`ACADEMIC_DATA_EXPIRING`** `{cohortId, courseName, closingDate, expirationDate, daysRemaining}` (preaviso de retención, PAR-16/17).

> La auditoría se publica en `audit.events` (T01 persiste); nombre/topic a confirmar con T11 en G1/G4. Nuestros eventos de configuración (`GLOBAL_CONFIGURATION_CHANGED`, `PARAMETER_CHANGED`, `MODEL_PROVIDER_CHANGED`) **quedan en inglés** y su topic de emisión se fija/registra en G1.

## Topics y particiones

| Topic | Eventos | Rol Backoffice | Partición |
|---|---|---|---|
| `system.notifications` | **ACADEMIC_DATA_EXPIRING** (+ avisos T11 a confirmar) | **Publica** | por `cohortId` |
| `challenges.results` | **hecho único T03** (resultado de desafío, XP/monedas y desglose) | **Consume** (read models engagement) | por `courseId` |
| `courses.lifecycle` | COURSE_CREATED, COURSE_ACTIVATED, COURSE_ARCHIVED, ROSTER_UPDATED | **Consume** (lectura) | por `courseId` |
| `audit.events` (v1) | auditoría RF-AUD-* | **Publica** (T01 persiste) | por `actorId` (header) |
| `identity.events` | AdminCreated/Deleted, AdminRecoveryExecuted, RoleChanged | Tema 01; payloads **pendientes de contrato** | por `actorId` |
| `retention.events` | RetentionDecisionCreated, DataAnonymized | Tema 01; payload acordado, schema externo **pendiente** | por `courseId` |
| `bank.events` | **ACCOUNT_BALANCE_CHANGED** (saldo por alumno/curso) | **Consume** (frescura; REST para replay) | por `courseId` |
| `roadmap.events`, `survey.events`, `ranking.events` | eventos de los Temas 10/02 | **Consume** (lectura) | por `courseId` |

> **Topics a registrar con T11 (G1):** `administration.events` (config del Backoffice), `audit.events`, `administration.events.dlt` (DLT). **No se crean topics nuevos sin T11.**
> **T08 (Banco):** además del **REST** (`/api/bank/**`) para replay/inicial, **nos suscribimos** al evento de actualización de saldo por alumno/curso (nombre/payload a confirmar con Banco; naming alineado con la lista de canales que publica **T11**).
> **T03 (Desafíos):** ingesta por **eventos** (`challenges.results` — hecho único), no por endpoints de agregación (acordado con T03).

Cada **consumer group** pertenece a un consumidor. Idempotencia por `event_id` y `version` (descarta `v <= local`). Los read models se reconstruyen vía **contratos de lectura (REST)**. **Frescura de lectura ≤ 15 min** (decisión de arquitectura).

## Outbox

Persistir el cambio y el `OutboxMessage` en la **misma transacción**; un publisher envía luego al topic. Evita "commit sin evento". T01 usa el mismo mecanismo (`outbox_events`).

### Resiliencia del publisher (US-02 T3) — backoff exponencial + Dead Letter Topic

- Cada fila lleva **`retry_count`** y **`next_attempt_at`** (NULL = lista ahora). El publisher solo toma filas cuya ventana de backoff venció (`findReadyToPublish`).
- Al fallar una publicación: se incrementa el contador y se agenda el siguiente intento con **backoff exponencial** `min(base × 2ⁿ, tope)` (durable en BD).
- Al **agotar el presupuesto** (`max-retries`), la fila se reenvía al **Dead Letter Topic** (`administration.events.dlt`, a **registrar con T11**). Si el DLT también falla, queda `PENDING` para reintentar el DLT en el próximo ciclo.
- Migración: `V3__outbox_message_retry.sql` (versión **V3** para no chocar con el `V2` de US-01).

## Idempotencia

Cada evento lleva `event_id`; el consumidor registra los procesados en `ProcessedEvent(event_id, consumer)` y ignora duplicados.

### Consumidor de referencia (US-02 T4) — validación del ciclo

- El Backoffice tiene un **consumidor de referencia** (`@KafkaListener` sobre `administration.events`) que valida el ciclo **Outbox → Kafka → consumo** y aplica **idempotencia por `eventId` + versión** (descarta duplicados y versiones no más nuevas por parámetro).
- El dedup del consumidor de referencia es **in-memory** (`ProcessedEventRegistry`); la persistencia durable `reporting.processed_event` es de **US-08** (Julieta).
- Serialización actual: `String` + `ObjectMapper` (el wire JSON es idéntico al `EventoDTO`); a alinear con `Event<T>` + `JsonDeserializer` tipado si el equipo lo decide (patrón del PDF de T11).

## Retención (acuerdo con T01)

Al recibir `DataAnonymized`/`RetentionDecisionCreated` (payload `entityType`/`entityId`), el Backoffice **alinea sus read models de reporting** (purga/anonimiza el dato correspondiente). **Nunca purga por su cuenta** ni inicia retención.

## Flujo de ejemplo

```text
Backoffice (Tema 12)
  │ EventoDTO{eventId, eventType, timestamp, producer: "backoffice-service", payload}
  │   config → topic a registrar con T11 (G1) · ACADEMIC_DATA_EXPIRING → system.notifications
  │   auditoría → audit.events (T01 persiste)
  ▼
Kafka
  ├──► Temas 03/05/07/08/10 (consumen la configuración)
  └──► Tema 01 — persiste auditoría (audit.events)
```

```text
Temas 02/03/05/07/08/10
  │ eventos de cohorte, resultados de desafíos, encuestas, práctica, evaluación, banco, roadmap
  ▼
Kafka (topics de cada tema: courses.lifecycle, challenges.results, …)
  ▼
Reporting & Analytics Service (consumer group: reporting → read models)
```

> ⚠️ **El Kafka y el naming de topics los gestiona T11 (Social y Notificaciones).** Todo contrato de mensajería se **ratifica con T11 (G1)** antes de implementarse; **no se crean topics nuevos**.
> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§11-§14, §12 topics) · **PDF de eventos de T11 (2026-09)**.