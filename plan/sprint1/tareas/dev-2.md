# Dev 2 (Mateo Carballo Juarez) — Tareas Sprint 1

> **Total:** 25h · **Código (BE+TEST):** 22h · **Capacidad real:** 50h (5h/día · 0 ausencias · 100%)
> **Rama/PR:** `feature/foundation-outbox` (PR a develop #1, merge Día 3).
> **Primera tarea a arrancar (Day 1):** **T2** — envelope estándar `GlobalConfigurationChanged` (base del contrato de eventos; sin dependencias).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): Outbox en la misma tx, idempotencia por `event_id`+versión, sin secretos.
- **SKILLS.md**: `SKILL-evento`, `SKILL-build-test`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/08-eventos-kafka.md`.

---

## US-02 · Propagación del cambio de parámetro (Outbox + Kafka + Caché TTL)

### T2 — [BACKEND] Envelope estándar `GlobalConfigurationChanged` · 4h
**Descripción (plan/tareas.md):** Clase del envelope: eventId, eventType, occurredAt, correlationId, actorId, source, payload {key, value, version}.
**Qué hacer:** definir el envelope `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload{key, value, version}}`.
**Refs:** SKILL-evento · `plan/CONTRATOS.md`.

### T3 — [BACKEND] Reintentos con backoff exponencial y Dead Letter Topic · 6h
**Descripción (plan/tareas.md):** Si Kafka no disponible, eventos quedan pendientes. Reintentos con backoff. Irrecuperables → DLT. Sin pérdida (CA2).
**Qué hacer:** ante broker caído, los eventos quedan pendientes y se reintentan con backoff; los irrecuperables van a DLT sin pérdida.
**CA relacionados:** CA2.

### T4 — [BACKEND] Idempotencia por `eventId` y versión (consumidor de referencia) · 4h
**Descripción (plan/tareas.md):** Tabla `ProcessedEvent(event_id, consumer)`. Descartar duplicados (CA4). Componente reutilizable, NO implementación en otros equipos.
**Qué hacer:** tabla `ProcessedEvent(event_id, consumer)`; descartar duplicados y versiones anteriores. Es un **componente de referencia** (no se implementa en los otros equipos).
**CA relacionados:** CA4.

### T5 — [TEST] Integración del ciclo completo Outbox → Kafka → consumo · 8h
**Descripción (plan/tareas.md):** Testcontainers (Kafka + PostgreSQL). Inserción en outbox → publicación → recepción.
**Qué hacer:** test con Testcontainers (Kafka + PostgreSQL): inserción en outbox → publicación → recepción/consumo.
**CA relacionados:** CA1, CA2.

### T9 — [REVISION] Peer review de concurrencia y transaccionalidad · 3h
**Descripción (plan/tareas.md):** Atomicidad de tx outbox, manejo de conexiones concurrentes, ausencia de race conditions.
**Qué hacer:** revisar atomicidad de la tx outbox, manejo de conexiones concurrentes y ausencia de race conditions; cerrar en Taiga.

## Criterios de aceptación (US-02)
- **CA1:** todo cambio de parámetro genera un aviso pendiente en la BD.
- **CA2:** el aviso se publica en Kafka y se marca enviado; si el broker no está, queda pendiente sin perderse.
- **CA3:** los consumidores actualizan al recibir el aviso o, a los 10 min, usan el último valor.
- **CA4:** un aviso repetido o de versión anterior no altera el valor vigente del consumidor.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Tests de integración verdes (Testcontainers).
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] sdd/docs actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-02 · T3 — [BACKEND] Reintentos con backoff exponencial y Dead Letter Topic
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T4 — [BACKEND] Idempotencia por eventId y versión
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T5 — [TEST] Integración del ciclo Outbox → Kafka → consumo
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T9 — [REVISION] Peer review de concurrencia y transaccionalidad
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

