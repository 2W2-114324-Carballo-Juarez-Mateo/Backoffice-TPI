# dev-2.md (Carballo Juarez, Mateo) - Tareas Sprint 1

> **Capacidad:** 41.5 h - **Asignado:** 25 h - **Repo:** 2026-P4-BE/tpi-backoffice (mono-modulo, 1 datasource + 2 esquemas)
> **Flujo:** feature/*|fix/* -> develop - release/*|hotfix/* -> main - PR con 1 aprobacion - sin push directo
> **Division pareja:** los 9 devs cubren las 5 capas (BACK + FRONT + TEST + REV + DOC) - nadie testea/revisa lo suyo.

- **[BACK]** US-02 T3 - Reintentos con backoff exponencial y Dead Letter Topic - 6h
- **[BACK]** US-02 T4 - Idempotencia por eventId y version en el consumidor de referencia - 4h
- **[FRONT]** FE-2a - Servicio HTTP de parametros (GET/PUT) - 4h
- **[TEST]** US-01 T8 - Tests de integracion con Testcontainers (US-01) - 6h
- **[REV]** US-03 T8b - Auditoria del manejo de errores (ErrorApi, trabajo de otro) - 1h
- **[DOC]** G1 - Solicitud de contrato de mensajeria a T11 (Dia 1) - 4h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).

## Registro de trabajo / trazabilidad

### US-02 T3 - Reintentos con backoff exponencial y Dead Letter Topic - ✅ HECHO

| Campo | Registro |
|---|---|
| **Estado** | ✅ hecho - pusheado a `feature/us-02-reliability` |
| **Qué se hizo** | Backoff exponencial durable por fila de outbox (`retry_count` + `next_attempt_at`) con tope máximo; cuando se agota el presupuesto de reintentos, la fila se reenvía a un **Dead Letter Topic**; si el DLT también falla, la fila queda `PENDING` para reintentar el DLT en el próximo ciclo |
| **Archivos/clases** | `db/migration/V3__outbox_message_retry.sql` · `OutboxMessage` (+retryCount, nextAttemptAt) · `OutboxStatus` (+DEAD_LETTER) · `OutboxMessageRepository` (+findReadyToPublish) · `OutboxPublisherServiceImpl` (backoff + DLT) · `OutboxPublisherProperties` (+maxRetries/backoff/dltTopic) · `application.properties`/test |
| **Decisiones / supuestos** | Migración en **V3** para no chocar con el V2 de US-01 (`global_parameter`) · `next_attempt_at` NULL = listo ahora · backoff `min(base*2^n, max)` sin overflow · el reintento es durable en BD (no in-memory) |
| **CA / RF cubiertos** | US-02 (CA4 orden por `param_key` preservado; resiliencia del publisher) · RULES "Outbox obligatorio" |
| **Tests agregados** | `OutboxPublisherServiceImplTest`: backoff dentro del presupuesto, envío a DLT al agotar retries, DLT no disponible → queda PENDING, interrupt flag, no propaga excepciones |
| **PR / commits** | `05530f4` (rama `feature/us-02-reliability`, sin PR todavía) |
| **Pendientes / deuda** | T5 (Máximo) integra el ciclo completo Outbox→Kafka→consumo · topic DLT (`administration.events.dlt`) a **registrar con T11 (G1)** |

### US-02 T4 - Idempotencia por eventId y version en el consumidor de referencia - ✅ HECHO

| Campo | Registro |
|---|---|
| **Estado** | ✅ hecho - pusheado a `feature/us-02-reliability` |
| **Qué se hizo** | Consumidor de referencia (`@KafkaListener` sobre `administration.events`) que valida el ciclo Outbox→Kafka→consumo y descarta duplicados por `eventId` y versiones no más nuevas por parámetro |
| **Archivos/clases** | `ProcessedEventRegistry` (interfaz) · `InMemoryProcessedEventRegistry` (dedup in-memory, `synchronized`) · `ReferenceConfigConsumer` · `KafkaConsumerConfig` (`@EnableKafka`) · `application.properties`/test (`outbox.consumer.group-id`, `auto-startup=false` en tests) |
| **Decisiones / supuestos** | Dedup **in-memory** (el store durable `reporting.processed_event` es de US-08/Julieta; este registry valida el ciclo sin depender de esa tabla) · se parsea el envelope con Jackson (`eventId` + `payload.paramKey/version`) para no depender del mapeo exacto de T2 |
| **CA / RF cubiertos** | US-02 idempotencia (`event_id` + versión) · RULES-eventos 3 |
| **Tests agregados** | `InMemoryProcessedEventRegistryTest` (duplicado por eventId, versión vieja/igual, versión nueva, parámetros independientes, eventId vacío/null) · `ReferenceConfigConsumerTest` (envelope válido → registry; malformado → no llega al registry) |
| **PR / commits** | `8c296b9` (rama `feature/us-02-reliability`, sin PR todavía) |
| **Pendientes / deuda** | Alinear a `Event<T>` + `JsonDeserializer` tipado si el equipo lo decide (hoy String+ObjectMapper, mismo wire JSON) · topic a **registrar con T11 (G1)** |

