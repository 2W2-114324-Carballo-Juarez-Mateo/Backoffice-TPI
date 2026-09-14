# Dev 1 — Tareas Sprint 1

> **Total:** 22h · **Código (BE+TEST):** 18h
> **Ramas/PRs:** `feature/foundation-outbox` (PR #1, merge Día 3) · Infra en `main`/rama de setup.

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): respetar sus reglas (Outbox en la misma tx, sin secretos, idempotencia, etc.).
- **SKILLS.md** (misma raíz): usar el procedimiento correspondiente (`SKILL-evento`, `SKILL-despliegue`, `SKILL-build-test`…).
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): cumplir **Nivel 0** (tarea) y **Nivel 1** (historia) antes de cerrar.
- **SDD:** `plan/sdd/backend/docs/08-eventos-kafka.md`, `09-despliegue.md`.
- **Contratos:** `plan/CONTRATOS.md` (evento `GlobalConfigurationChanged`).

---

## Infra · Tarea transversal
### T0 — [INFRA] Scaffolding Maven multi-módulo + Docker Compose (PostgreSQL, Kafka, Eureka) · 6h
**Qué hacer:** dejar el esqueleto del multi-módulo Maven + `docker-compose` (2 PostgreSQL, Kafka, Eureka, Config, Gateway) funcionando. Es el **requisito previo** para que arranque el resto.
**Refs:** SKILL-despliegue · RULES-stack.

## US-02 · Propagación del cambio de parámetro (Outbox + Kafka + Caché TTL)

### T1a — [BACKEND] Migración Flyway de `outbox_events` · 3h
**Qué hacer:** crear la tabla `outbox_events` (`id`, `event_type`, `payload`, `status`, `created_at`) en `administration_db` con una migración Flyway. **Prerequisito compartido → mergear Día 1** (desbloquea US-01 T4).
**Refs:** SKILL-despliegue (Flyway).

### T1b — [BACKEND] Publisher programado (Outbox → Kafka) · 5h
**Qué hacer:** `@Scheduled` que lee los pendientes de `outbox_events`, publica en el topic `administration.events` y los marca como enviados.
**CA relacionados:** CA1, CA2 de US-02.

### T2 — [BACKEND] Envelope estándar `GlobalConfigurationChanged` · 4h
**Qué hacer:** definir el envelope `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload{key, value, version}}`.
**Refs:** SKILL-evento · `plan/CONTRATOS.md`.

### T8 — [DOCUMENTACION] Envelope, catálogo de topics y contrato del consumidor · 4h
**Qué hacer:** documentar el envelope + topics + guía para que T03/05/08/10 implementen su consumidor con caché TTL 10 min.

## Criterios de aceptación (US-02)
- **CA1:** todo cambio de parámetro genera un aviso pendiente en la BD.
- **CA2:** el aviso se publica en Kafka y se marca enviado; si el broker no está, queda pendiente sin perderse.
- **CA3:** los consumidores actualizan al recibir el aviso o, a los 10 min, usan el último valor.
- **CA4:** un aviso repetido o de versión anterior no altera el valor vigente del consumidor.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Migración Flyway aplicada y testeada.
- [ ] Tests de la tarea verdes.
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado (T9 lo hace Dev 2).
- [ ] sdd/docs actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### Infra · T0 — [INFRA] Scaffolding Maven multi-módulo + Docker Compose
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T1a — [BACKEND] Migración Flyway `outbox_events`
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T1b — [BACKEND] Publisher programado (Outbox → Kafka)
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T2 — [BACKEND] Envelope estándar `GlobalConfigurationChanged`
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-02 · T8 — [DOCUMENTACION] Envelope, topics y contrato del consumidor
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

