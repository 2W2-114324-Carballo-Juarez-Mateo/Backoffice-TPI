# RULES — Eventos (Kafka)

1. **Envelope obligatorio (PDF de T11):** todo evento usa **`EventoDTO{eventId, eventType, timestamp, producer, payload}`** (5 campos, genérico `Event<T>`). **Todo en inglés** (SCREAMING_SNAKE_CASE). `producer = spring.application.name` (**`backoffice-service`**). `correlationId/actorId/role` → **headers de Kafka**. Ver `docs/08-eventos-kafka.md`.
2. **Outbox:** publicar el evento **en la misma transacción** que el cambio (tabla `OutboxMessage`). Nunca publiques a Kafka directo tras un commit sin outbox.
3. **Idempotencia:** el consumidor registra `event_id` procesados y **ignora duplicados** (at-least-once).
4. **Topic + consumer group por servicio.** El BackOffice **publica** en `system.notifications` (ACADEMIC_DATA_EXPIRING), `audit.events` y topic de config; **consume** `challenges.results`, `courses.lifecycle`, `bank.events`, `roadmap.events`, `survey.events`, `ranking.events`. **NO se crean topics nuevos: se registran con T11 (G1)** (`administration.events`, `audit.events`, `administration.events.dlt`).
5. **No reenviar credenciales del usuario** en eventos; solo IDs y payload de negocio.
6. El evento debe ser **descriptivo y estable**: cambiar el `eventType` o el payload rompe consumidores (contract).
7. Audit y Reporting consumen eventos; un cambio de payload debe respetar el contrato versionado.
8. **Coordinación obligatoria con T11 (G1):** cualquier topic, nombre de evento o payload nuevo se ratifica con el grupo de Notificaciones antes de implementarse.