# RULES — Eventos (Kafka)

1. **Envelope obligatorio (Drive oficial):** todo evento usa **`EventoDTO{eventId, eventType, timestamp, producer, payload}`** (5 campos). `eventType` en **español** (SCREAMING_SNAKE_CASE). `producer = tema-12-backoffice`. `correlationId/actorId/role` → **headers de Kafka**. Ver `docs/08-eventos-kafka.md`.
2. **Outbox:** publicar el evento **en la misma transacción** que el cambio (tabla `OutboxMessage`). Nunca publiques a Kafka directo tras un commit sin outbox.
3. **Idempotencia:** el consumidor registra `event_id` procesados y **ignora duplicados** (at-least-once).
4. **Topic + consumer group por servicio.** El BackOffice **publica** en `sistema.notificaciones` (VENCIMIENTO_DATOS_ACADEMICOS), `audit.events` (a confirmar vs Drive) y topic de config a fijar; **consume** `desafios.resultados`, `cursos.ciclo-vida`, `bank.events`, `survey.events`, `ranking.events`, `roadmap.events`. **No inventes topics** sin revisar la tabla de topics ni sin validación de **T11** (dueño del Kafka).
5. **No reenviar credenciales del usuario** en eventos; solo IDs y payload de negocio.
6. El evento debe ser **descriptivo y estable**: cambiar el `eventType` o el payload rompe consumidores (contract).
7. Audit y Reporting consumen eventos; un cambio de payload debe respetar el contrato versionado.
8. **Coordinación obligatoria con T11 (G1):** cualquier topic, nombre de evento o payload nuevo se ratifica con el grupo de Notificaciones antes de implementarse.