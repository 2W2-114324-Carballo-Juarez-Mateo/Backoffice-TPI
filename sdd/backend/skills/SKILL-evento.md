# SKILL — Publicar y consumir un evento (Kafka + Outbox)

## Publicar (productor)

1. En el **dominio**: definí el evento (ej. `GlobalConfigurationChanged`) en `domain/events` con los campos del envelope.
2. En el **caso de uso**: tras persistir el cambio, creá el `OutboxMessage` (`eventType`, `payload` como JSON) **dentro de la misma transacción**.
3. En **infraestructura**: el publisher lee los `OutboxMessage` pendientes y los envía al topic correcto (`administration.events`), marcándolos como publicados.
4. El topic debe existir (naming por dominio y partición por `key` — ver `docs/08-eventos-kafka.md`).

## Consumir

1. Anotá el listener con el consumer group del servicio (ej. `reporting`).
2. **Idempotencia:** antes de procesar, verificá `ProcessedEvent`; si ya se procesó el `event_id`, ignorá.
3. Convertí el payload a DTO y actualizá tu read model (Reporting) o registrá auditoría (Audit).
4. Para consumir dominios ajenos (`course.events`, `gamification.events`, `ranking.events`, `survey.events`), usá el topic **sin** escribir en sus bases.

## Reglas

- Nunca publiques a Kafka directo sin Outbox.
- El envelope y el `eventType` son contrato: no los cambies sin coordinar consumidores.
- El BackOffice **no publica** eventos de cursos/desafíos/usuarios (esos dominios son de otros equipos).
- Probá el flujo completo con Testcontainers Kafka.

> Ver `rules/RULES-eventos.md` y `docs/08-eventos-kafka.md`.