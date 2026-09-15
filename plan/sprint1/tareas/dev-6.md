# Dev 6 (Valentina Maldonado) — Tareas Sprint 1

> **Total:** 22h · **Código (BE+TEST):** 16h · **Capacidad real:** 42,5h (5h/día · 0 ausencias · 85%)
> **Primera tarea a arrancar (Day 1):** **US-08 T6** — mapear contratos de lectura (primera de US-08).
> **Rama/PR:** `feature/us-08-ingesta-kafka` (PR a develop #3, merge Día 6).
> **Depende de:** Nada (microservicio y BD aislados).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): idempotencia por `event_id`, DLT, encuestas solo agregados anónimos.
- **SKILLS.md**: `SKILL-evento`, `SKILL-caso-uso`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/08-eventos-kafka.md`.
- **Contratos:** `plan/CONTRATOS.md` (temas de lectura T02/04/05/07/08/10).

---

## US-08 · Ingesta de datos de los temas con deduplicación

### T2a — [BACKEND] Consumidores de Kafka para T02 / T04 / T05 · 6h
**Descripción (plan/tareas.md):** Parte de US-08 T2: consumer groups + **adapter por tema** para **T02, T04, T05** (transformar payload en read model).
**Qué hacer:** consumer group + un **adapter por tema** que transforma el payload en read model (Cursos/Matrícula, Teóricos/Encuestas, Prácticos).
**CA relacionados:** CA1.

### T2b — [BACKEND] Consumidores de Kafka para T06 / T07 / T10 · 6h
**Descripción (plan/tareas.md):** Parte de US-08 T2: consumer groups + **adapter por tema** para **T06, T07, T10** (transformar payload en read model). **T08 (Banco) se ingesta por REST** (`/api/bank/**`, polling ≤15 min), no por Kafka (acordado con Banco).
**Qué hacer:** idem para Sandbox (T06), Evaluación LLM (T07), Roadmap (T10). El adapter de **Banco (T08)** es un **cliente REST** que sincroniza read models por polling (no un consumer Kafka).
**CA relacionados:** CA1.

### T3 — [BACKEND] Deduplicación por `eventId` en cada consumidor · 4h
**Descripción (plan/tareas.md):** Verificar en processed_events. Si existe → descartar (CA2). Si no → procesar y registrar (CA1).
**Qué hacer:** verificar en `processed_events`; si existe → descartar; si no → procesar y registrar.
**CA relacionados:** CA1, CA2.

### T6 — [DOCUMENTACION] Mapear contratos de lectura y esquemas JSON de los 6 temas · 6h
**Descripción (plan/tareas.md):** Campos consumidos por tema. Consumer groups en docs/08. Entregable de coordinación con los otros equipos.
**Qué hacer:** documentar los campos consumidos por tema y los consumer groups (entregable de coordinación con los otros equipos).

## Criterios de aceptación (US-08)
- **CA1:** un dato nuevo actualiza los reportes y se registra su identificador.
- **CA2:** un dato repetido se descarta sin recalcular.
- **CA3:** un dato mal formado va a la cola de descarte y no detiene el resto.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Consumidores idempotentes.
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] sdd/docs actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-08 · T2a — [BACKEND] Consumidores de Kafka para T02/T04/T05
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-08 · T2b — [BACKEND] Consumidores de Kafka para T06/T07/T08/T10
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-08 · T3 — [BACKEND] Deduplicación por eventId
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-08 · T6 — [DOCUMENTACION] Mapear contratos de lectura de los 6 temas
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

