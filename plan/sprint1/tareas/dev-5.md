# Dev 5 (Julieta Ariadna Disca) — Tareas Sprint 1

> **Total:** 21h · **Código (BE+TEST):** 18h · **Capacidad real:** 60h (6h/día · 0 ausencias · 100%)
> **Primera tarea a arrancar (Day 1):** **US-08 T1** — migración `ProcessedEvent` (reporting aislado).
> **Ramas/PRs:** `feature/foundation-outbox` (PR a develop #1) · `feature/us-08-ingesta-kafka` (PR a develop #3) · `feature/us-03-gateway-auth` (PR a develop #2).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): idempotencia por `event_id`, DLT sin bloquear, Database per Service.
- **SKILLS.md**: `SKILL-evento`, `SKILL-build-test`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/08-eventos-kafka.md`.
- **Contratos:** `plan/CONTRATOS.md`.

---

## US-02 · Propagación del cambio de parámetro (Outbox + Kafka)

### T6 — [TEST] Validar resiliencia ante caída del broker de Kafka · 6h
**Descripción (plan/tareas.md):** Simular desconexión: eventos pendientes en outbox, reenvío al restaurar (CA2, BDD Esc. 2).
**Qué hacer:** simular desconexión: eventos pendientes en outbox y reenvío al restaurar (sin pérdida).
**CA relacionados:** CA2.

### T7 — [TEST] Validar descarte de eventos duplicados y de versión anterior · 4h
**Descripción (plan/tareas.md):** Mismo eventId dos veces → segundo ignorado (CA4).
**Qué hacer:** mismo `eventId` dos veces → el segundo se ignora; versión anterior no sobreescribe.
**CA relacionados:** CA4.

## US-08 · Ingesta de datos de los temas con deduplicación

### T1 — [BACKEND] Migración Flyway y tabla de deduplicación `ProcessedEvent` · 4h
**Descripción (plan/tareas.md):** DDL en reporting_db. Tabla processed_events(event_id UUID PK, consumer VARCHAR, processed_at TIMESTAMP).
**Qué hacer:** `reporting_db` → tabla `processed_events(event_id UUID PK, consumer, processed_at)`.
**CA relacionados:** CA1, CA2.

### T4 — [BACKEND] Dead Letter Topic para eventos malformados · 4h
**Descripción (plan/tareas.md):** Mensajes con errores de formato van a DLT sin bloquear el partition consumer (CA3).
**Qué hacer:** configurar DLT para mensajes malformados sin bloquear el partition consumer.
**CA relacionados:** CA3.

## US-03 · Consola de gestión administrativa (integración de identidades)

### T8 — [REVISION] Auditoría de fronteras de microservicios y seguridad en PR · 3h
**Descripción (plan/tareas.md):** Garantizar que no se creó ninguna tabla de usuarios en administration_db y que la arquitectura permanece limpia.
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

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-02 · T6 — [TEST] Resiliencia ante caída del broker de Kafka
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T7 — [TEST] Descarte de duplicados y de versión anterior
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-08 · T1 — [BACKEND] Migración y tabla ProcessedEvent
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-08 · T4 — [BACKEND] Dead Letter Topic para malformados
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-03 · T8 — [REVISION] Auditoría de fronteras de microservicios
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### Resumen del integrante
- **Tareas completadas:** 0/5 · **Horas reales:** __ / __ h
- **Notas generales:**

