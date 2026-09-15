# Dev 1 (Luciano Paz) — Tareas Sprint 1

> **Total:** 18h · **Código (BE+TEST):** 14h · **Capacidad real:** 47,5h (5h/día · 0 ausencias · 95%)
> **Ramas/PRs:** `feature/foundation-outbox` (PR a develop #1, merge Día 3) · Infra en `main`/rama de setup.
> **Primera tarea a arrancar (Day 1):** **T1a** — migración `outbox_message` (merge Día 1, desbloquea al resto).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): respetar sus reglas (Outbox en la misma tx, sin secretos, idempotencia, etc.).
- **SKILLS.md** (misma raíz): usar el procedimiento correspondiente (`SKILL-evento`, `SKILL-despliegue`, `SKILL-build-test`…).
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): cumplir **Nivel 0** (tarea) y **Nivel 1** (historia) antes de cerrar.
- **SDD:** `plan/sdd/backend/docs/08-eventos-kafka.md`, `09-despliegue.md`.
- **Contratos:** `plan/CONTRATOS.md` (evento `GlobalConfigurationChanged`).

---

## Infra · Tarea transversal
### T0 — [INFRA] Scaffolding Maven multi-módulo + Docker Compose (PostgreSQL, Kafka, Eureka) · 6h
**Descripción (plan/tareas.md):** Scaffolding del multi-módulo Maven + Docker Compose (PostgreSQL, Kafka, Eureka). Requisito previo del sprint.
**Qué hacer:** dejar el esqueleto del multi-módulo Maven + `docker-compose` (2 PostgreSQL, Kafka, Eureka, Config, Gateway) funcionando. Es el **requisito previo** para que arranque el resto.
**Refs:** SKILL-despliegue · RULES-stack.

## US-02 · Propagación del cambio de parámetro (Outbox + Kafka + Caché TTL)

### T1a — [BACKEND] Migración Flyway de `outbox_message` · 3h
**Descripción (plan/tareas.md):** Parte de US-02 T1: **DDL y entidad `OutboxMessage`** (tabla `outbox_message`). El publisher va en T1b.
**Qué hacer:** crear la tabla `outbox_message` (`id`, `event_type`, `payload`, `status`, `created_at`) en `administration_db` con una migración Flyway. **Prerequisito compartido → mergear Día 1** (desbloquea US-01 T4).
**Refs:** SKILL-despliegue (Flyway).

### T1b — [BACKEND] Publisher programado (Outbox → Kafka) · 5h
**Descripción (plan/tareas.md):** Parte de US-02 T1: **worker `@Scheduled`** que lee pendientes, publica en `administration.events` y marca enviado.
**Qué hacer:** `@Scheduled` que lee los pendientes de `outbox_message`, publica en el topic `administration.events` y los marca como enviados.
**CA relacionados:** CA1, CA2 de US-02.

### T8 — [DOCUMENTACION] Envelope, catálogo de topics y contrato del consumidor · 4h
**Descripción (plan/tareas.md):** Schema JSON, topic `administration.events`, guía para que Temas 03/05/08/10 implementen su consumidor con caché TTL 10 min.
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

### US-02 · T1a — [BACKEND] Migración Flyway `outbox_message`
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
- **Tareas completadas:** 0/4 · **Horas reales:** __ / __ h
- **Notas generales:**

