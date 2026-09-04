# RULES — Eventos (Kafka)

1. **Envelope obligatorio:** todo evento usa el formato `{eventId, eventType, occurredAt, correlationId, actorId, source, payload}` (ver `docs/08-eventos-kafka.md`).
2. **Outbox:** publicar el evento **en la misma transacción** que el cambio (tabla `OutboxMessage`). Nunca publiques a Kafka directo tras un commit sin outbox.
3. **Idempotencia:** el consumidor registra `event_id` procesados y **ignora duplicados** (at-least-once).
4. **Topic por dominio + consumer group por servicio**. El BackOffice **publica** en `identity.events`, `administration.events`, `audit.events`, `retention.events` y **consume** `course.events`, `gamification.events`, `ranking.events`, `survey.events`. No inventes topics nuevos sin revisar la tabla de topics.
5. **No reenviar credenciales del usuario** en eventos; solo IDs y payload de negocio.
6. El evento debe ser **descriptivo y estable**: cambiar el `eventType` o el payload rompe consumidores (contract).
7. Audit y Reporting consumen eventos; un cambio de payload debe respetar el contrato versionado.