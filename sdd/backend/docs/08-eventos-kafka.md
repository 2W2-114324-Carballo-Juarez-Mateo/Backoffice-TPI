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
  "source": "administration-service",
  "payload": {}
}
```

## Topics y particiones

| Topic | Eventos | Rol Backoffice | Partición |
|---|---|---|---|
| `administration.events` | GlobalConfigurationChanged, ModelProviderChanged, ModelFunctionChanged | **Publica** | por `key` |
| `identity.events` | AdminCreated/Deleted, RoleChanged, auditoría | Tema 01 (consume/consulta) | por `actorId` |
| `course.events` | CourseCreated/Activated/Archived, matrícula | **Consume** (lectura) | por `courseId` |
| `survey.events`, `ranking.events`, `bank.events`, `roadmap.events`, `challenge.events` | eventos de los Temas 04/10/08/02/03/05/07 | **Consume** (lectura) | por `courseId` |

Cada **consumer group** pertenece a un consumidor. Idempotencia por `event_id` y `version` (descarta `v <= local`). Los read models se reconstruyen vía **contratos de lectura (REST)**. **Frescura de lectura ≤ 15 min.**

## Outbox

Persistir el cambio y el `OutboxMessage` en la **misma transacción**; un publisher envía luego al topic. Evita "commit sin evento".

## Idempotencia

Cada evento lleva `event_id`; el consumidor registra los procesados en `ProcessedEvent(event_id, consumer)` y ignora duplicados.

## Flujo de ejemplo

```text
Administration & Configuration Service
  │ GlobalConfigurationChanged / ModelProviderChanged
  ▼
Kafka (administration.events)
  ├──► Tema 01 — auditoría (persiste)
  └──► Temas 03/05/07/08/10 (consumen la configuración)
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