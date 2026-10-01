# RULES — Eventos (Kafka)

1. **Envelope obligatorio (T11/cátedra):** todo evento usa **`EventEnvelope<T>{eventId, eventType, eventVersion, timestamp, producer, payload}`** (6 campos, genérico con payload tipado). **Todo en inglés** (SCREAMING_SNAKE_CASE). `eventVersion` = versión del contrato del evento. `producer` = **`tema-12-backoffice-service`** (constante del contrato, independiente de `spring.application.name` = `backoffice-service`). `correlationId/actorId/role` → **dentro del `payload`** (solo `traceparent` como header). Ver `docs/08-eventos-kafka.md`.
2. **Outbox:** publicar el evento **en la misma transacción** que el cambio (tabla `OutboxMessage`). Nunca publiques a Kafka directo tras un commit sin outbox.
3. **Idempotencia:** el consumidor registra `event_id` procesados y **ignora duplicados** (at-least-once).
4. **Topic + consumer group por servicio.** El BackOffice **publica** en `notifications.events` (ACADEMIC_DATA_EXPIRING), `identity.audit.events` y topic de config; **consume** `challenges.events`, `courses.events`, `accounting.events`, `sandbox.events`, `survey.events`, `ranking.events`. **NO se crean topics nuevos: se registran con T11 (G1)** (`administration.events`, `identity.audit.events`, `administration.events.DLT`).
5. **No reenviar credenciales del usuario** en eventos; solo IDs y payload de negocio.
6. El evento debe ser **descriptivo y estable**: cambiar el `eventType` o el payload rompe consumidores (contract).
7. Audit y Reporting consumen eventos; un cambio de payload debe respetar el contrato versionado.
8. **Coordinación obligatoria con T11 (G1):** cualquier topic, nombre de evento o payload nuevo se ratifica con el grupo de Notificaciones antes de implementarse.