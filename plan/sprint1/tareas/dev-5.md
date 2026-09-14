# Dev 5 — Tareas Sprint 1

> **Total:** 21h · **Código (BE+TEST):** 18h
> **Ramas/PRs:** `feature/foundation-outbox` (PR #1) · `feature/us-08-ingesta-kafka` (PR #3) · `feature/us-03-gateway-auth` (PR #2).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): idempotencia por `event_id`, DLT sin bloquear, Database per Service.
- **SKILLS.md**: `SKILL-evento`, `SKILL-build-test`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/08-eventos-kafka.md`.
- **Contratos:** `plan/CONTRATOS.md`.

---

## US-02 · Propagación del cambio de parámetro (Outbox + Kafka)

### T6 — [TEST] Validar resiliencia ante caída del broker de Kafka · 6h
**Qué hacer:** simular desconexión: eventos pendientes en outbox y reenvío al restaurar (sin pérdida).
**CA relacionados:** CA2.

### T7 — [TEST] Validar descarte de eventos duplicados y de versión anterior · 4h
**Qué hacer:** mismo `eventId` dos veces → el segundo se ignora; versión anterior no sobreescribe.
**CA relacionados:** CA4.

## US-08 · Ingesta de datos de los temas con deduplicación

### T1 — [BACKEND] Migración Flyway y tabla de deduplicación `ProcessedEvent` · 4h
**Qué hacer:** `reporting_db` → tabla `processed_events(event_id UUID PK, consumer, processed_at)`.
**CA relacionados:** CA1, CA2.

### T4 — [BACKEND] Dead Letter Topic para eventos malformados · 4h
**Qué hacer:** configurar DLT para mensajes malformados sin bloquear el partition consumer.
**CA relacionados:** CA3.

## US-03 · Consola de gestión administrativa (integración de identidades)

### T8 — [REVISION] Auditoría de fronteras de microservicios y seguridad en PR · 3h
**Qué hacer:** garantizar que **no se creó ninguna tabla de usuarios** en `administration_db` y que las fronteras están limpias.

## Criterios de aceptación (US-08)
- **CA1:** un dato nuevo actualiza los reportes y se registra su identificador.
- **CA2:** un dato repetido se descarta sin recalcular.
- **CA3:** un dato mal formado va a la cola de descarte y no detiene el resto.

## Criterios de aceptación (US-02)
- **CA2:** el aviso se publica y se marca enviado; si el broker no está, queda pendiente sin perderse.
- **CA4:** un aviso repetido o de versión anterior no altera el valor vigente.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Tests verdes (Testcontainers Kafka + PostgreSQL).
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] sdd/docs actualizados.