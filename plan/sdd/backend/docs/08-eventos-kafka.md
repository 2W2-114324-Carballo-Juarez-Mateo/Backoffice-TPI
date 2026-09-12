# 08 — Eventos con Kafka

Broker elegido: **Kafka** (ADR-003). **RabbitMQ** queda como alternativa. Mismos patrones de confiabilidad: **Outbox + at-least-once + idempotencia**.

## Envelope común

```json
{
  "eventId": "uuid",
  "eventType": "ModelProviderChanged",
  "occurredAt": "2026-08-28T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "role": "ADMIN",
  "source": "administration-service",
  "payload": {}
}
```

> **Contrato T01:** `role` se agrega al envelope como **estándar de plataforma** (T01 lo confirma formalmente). Los eventos de auditoría que publica Tema 01 también lo llevan.

## Topics y particiones

| Topic | Eventos | Rol Backoffice | Partición |
|---|---|---|---|
| `administration.events` | GlobalConfigurationChanged, ModelProviderChanged, ModelFunctionChanged | **Publica** | por `key` |
| `audit.events` (v1) | ParameterChanged, ModelProviderChanged, EvaluatorActivated, GlobalRead (auditoría RF-AUD-*) | **Publica** (T01 persiste) | por `actorId` |
| `identity.events` | AdminCreated/Deleted, AdminRecoveryExecuted, RoleChanged | Tema 01; payloads **pendientes de contrato** | por `actorId` |
| `retention.events` | RetentionDecisionCreated, DataAnonymized | Tema 01; payload acordado, schema externo **pendiente** | por `courseId` |
| `course.events` | CourseCreated/Activated/Archived, matrícula | **Consume** (lectura) | por `courseId` |
| `survey.events`, `ranking.events`, `bank.events`, `roadmap.events`, `challenge.events` | eventos de los Temas 04/10/08/02/03/05/07 | **Consume** (lectura) | por `courseId` |

Cada **consumer group** pertenece a un consumidor. Idempotencia por `event_id` y `version` (descarta `v <= local`). Los read models se reconstruyen vía **contratos de lectura (REST)**. **Frescura de lectura ≤ 15 min.**

## Outbox

Persistir el cambio y el `OutboxMessage` en la **misma transacción**; un publisher envía luego al topic. Evita "commit sin evento". T01 usa el mismo mecanismo (`outbox_events`).

## Idempotencia

Cada evento lleva `event_id`; el consumidor registra los procesados en `ProcessedEvent(event_id, consumer)` y ignora duplicados.

## Retención (acuerdo con T01)

Al recibir `DataAnonymized`/`RetentionDecisionCreated` (payload `entityType`/`entityId`), el Backoffice **alinea sus read models de reporting** (purga/anonimiza el dato correspondiente). **Nunca purga por su cuenta** ni inicia retención.

## Flujo de ejemplo

```text
Administration & Configuration Service
  │ GlobalConfigurationChanged / ModelProviderChanged (administration.events)
  │ ParameterChanged / ModelProviderChanged / EvaluatorActivated / GlobalRead (audit.events)
  ▼
Kafka
  ├──► Temas 03/05/07/08/10 (consumen la configuración)
  └──► Tema 01 — persiste auditoría (audit.events)
```

```text
Temas 02/04/05/07/08/10
  │ eventos de cohorte, encuestas, práctica, evaluación, banco, roadmap
  ▼
Kafka (topics de cada tema)
  ▼
Reporting & Analytics Service (consumer group: reporting → read models)
```

> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§11-§14, §12 topics).