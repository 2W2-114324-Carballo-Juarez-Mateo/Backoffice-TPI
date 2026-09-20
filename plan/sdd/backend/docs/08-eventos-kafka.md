# 08 — Eventos con Kafka

Broker elegido: **Kafka** (ADR-003). **RabbitMQ** queda como alternativa. Mismos patrones de confiabilidad: **Outbox + at-least-once + idempotencia**.

## Envelope común — `EventoDTO` (Drive oficial, ✅ CERRADO)

```json
{
  "eventId": "123e4567-e89b-12d3-a456-426614174000",
  "eventType": "NOMBRE_DEL_EVENTO",
  "timestamp": "2026-09-02T19:30:00Z",
  "producer": "tema-12-backoffice",
  "payload": {}
}
```

- **5 campos** y nada más en el body. `eventType` en **español** (SCREAMING_SNAKE_CASE). `producer = tema-XX-nombre`.
- **`correlationId` / `actorId` / `role` → headers de Kafka** (trazabilidad y auditoría; a confirmar formalmente con T01/T11). No viajan en el body.
- Backoffice emite a **`sistema.notificaciones`**: **`VENCIMIENTO_DATOS_ACADEMICOS`** `{cohortId, courseName, closingDate, expirationDate, daysRemaining}` (preaviso de retención, PAR-16/17).

> La auditoría se publica en `audit.events` (T01 persiste); nombre/topic a confirmar vs Drive en G1/G4. Nuestros eventos de configuración (`GlobalConfigurationChanged`, `ParameterChanged`, `ModelProviderChanged`) **se renombran a español** y su topic de emisión se fija en G1.

## Topics y particiones

| Topic | Eventos | Rol Backoffice | Partición |
|---|---|---|---|
| `sistema.notificaciones` | **VENCIMIENTO_DATOS_ACADEMICOS** (+ avisos T11 a confirmar) | **Publica** | por `cohortId` |
| `desafios.resultados` | **hecho único T03** (resultado de desafío, XP/monedas y desglose) | **Consume** (read models engagement) | por `courseId` |
| `cursos.ciclo-vida` | NUEVO_CURSO_DISPONIBLE, CURSO_EN_RIESGO, CURSO_ARCHIVADO, matrícula | **Consume** (lectura) | por `courseId` |
| `audit.events` (v1) | auditoría RF-AUD-* | **Publica** (T01 persiste) | por `actorId` (header) |
| `identity.events` | AdminCreated/Deleted, AdminRecoveryExecuted, RoleChanged | Tema 01; payloads **pendientes de contrato** | por `actorId` |
| `retention.events` | RetentionDecisionCreated, DataAnonymized | Tema 01; payload acordado, schema externo **pendiente** | por `courseId` |
| `bank.events` | **AccountBalanceChanged** (saldo por alumno/curso) | **Consume** (frescura; REST para replay) | por `courseId` |
| `survey.events`, `ranking.events`, `roadmap.events` | eventos de los Temas 02 (encuestas CSAT)/10 | **Consume** (lectura) | por `courseId` |

> **Topics a confirmar vs Drive (G1):** `administration.events` (config del Backoffice, nombre a fijar), `audit.events`, `identity.events`, `retention.events`, `bank.events`, `survey.events`, `ranking.events`, `roadmap.events`.
> **T08 (Banco):** además del **REST** (`/api/bank/**`) para replay/inicial, **nos suscribimos** al evento de actualización de saldo por alumno/curso (nombre/payload a confirmar con Banco; naming alineado con la lista de canales que publica **T11**).
> **T03 (Desafíos):** ingesta por **eventos** (`desafios.resultados` — hecho único), no por endpoints de agregación (acordado con T03).

Cada **consumer group** pertenece a un consumidor. Idempotencia por `event_id` y `version` (descarta `v <= local`). Los read models se reconstruyen vía **contratos de lectura (REST)**. **Frescura de lectura ≤ 15 min** (decisión de arquitectura).

## Outbox

Persistir el cambio y el `OutboxMessage` en la **misma transacción**; un publisher envía luego al topic. Evita "commit sin evento". T01 usa el mismo mecanismo (`outbox_events`).

## Idempotencia

Cada evento lleva `event_id`; el consumidor registra los procesados en `ProcessedEvent(event_id, consumer)` y ignora duplicados.

## Retención (acuerdo con T01)

Al recibir `DataAnonymized`/`RetentionDecisionCreated` (payload `entityType`/`entityId`), el Backoffice **alinea sus read models de reporting** (purga/anonimiza el dato correspondiente). **Nunca purga por su cuenta** ni inicia retención.

## Flujo de ejemplo

```text
Backoffice (Tema 12)
  │ EventoDTO{eventId, eventType, timestamp, producer: "tema-12-backoffice", payload}
  │   config → topic de emisión a fijar (G1) · VENCIMIENTO_DATOS_ACADEMICOS → sistema.notificaciones
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
Kafka (topics de cada tema: cursos.ciclo-vida, desafios.resultados, …)
  ▼
Reporting & Analytics Service (consumer group: reporting → read models)
```

> ⚠️ **El Kafka y el naming de topics los gestiona T11 (Social y Notificaciones).** Todo contrato de mensajería se **ratifica con T11 (G1)** antes de implementarse.
> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§11-§14, §12 topics) · Drive oficial de eventos (2026-09).