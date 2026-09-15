# Dev 3 (Damian Gabriel Baigorria) — Tareas Sprint 1

> **Total:** 21h · **Código (BE+TEST):** 18h · **Capacidad real:** 40h (5h/día · 2 ausencias · 100%)
> **Primera tarea a arrancar (Day 1):** **T1** — migración `GlobalParameter` + seed PAR-01..18 (desbloquea a Dev 4).
> **Rama/PR:** `feature/us-01-parametros` (PR a develop #4, merge Día 7).
> **Depende de:** US-02 T1a (tabla `outbox_message`, merge Día 1 — Dev 1).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): configuración **solo hacia adelante** (RF-CFG-06), ADMIN-only, sin secretos.
- **SKILLS.md**: `SKILL-endpoint`, `SKILL-caso-uso`, `SKILL-build-test`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/05-endpoints.md`, `04-modelo-datos.md`.
- **Parámetros:** `plan/PARAMETROS.md`.

---

## US-01 · Modificación y versionado de parámetros globales

### T1 — [BACKEND] Migración Flyway y entidad `GlobalParameter` con historial · 6h
**Descripción (plan/tareas.md):** Script DDL, entidad JPA con key, value (jsonb), version (int). Seed de PAR-01..18.
**Qué hacer:** tabla `global_parameter` (`key`, `value` jsonb, `version`, `updated_by/at`) + entidad JPA + repositorio, con **seed PAR-01..18** (defaults del PRD).
**CA relacionados:** CA1.

### T2 — [BACKEND] Caso de uso `UpdateParameterCommand` con versionado y vigencia no retroactiva · 6h
**Descripción (plan/tareas.md):** Incrementa versión, valida rango, rechaza fechas retroactivas (RF-CFG-06), soporta Idempotency-Key (CA2).
**Qué hacer:** incrementa versión, valida rango, rechaza retroactividad (RF-CFG-06) y soporta **Idempotency-Key**.
**CA relacionados:** CA1, CA2, CA4.

### T7 — [TEST] Tests unitarios de dominio: versionado, idempotencia y vigencia · 6h
**Descripción (plan/tareas.md):** Incremento de versión (CA1), idempotencia por clave repetida (CA2), rechazo de fecha retroactiva y rango (CA4).
**Qué hacer:** incremento de versión (CA1), idempotencia por clave repetida (CA2), rechazo de rango/retroactividad (CA4).
**CA relacionados:** CA1, CA2, CA4.

### T10 — [REVISION] Peer review de PR, validación de DoD Nivel 1 y cierre en Taiga · 3h
**Descripción (plan/tareas.md):** Clean Architecture, PMD/Checkstyle, sin secretos, build CI verde.
**Qué hacer:** revisar el PR de la historia, validar DoD Nivel 1 y cerrar en Taiga.

## Criterios de aceptación (US-01)
- **CA1:** al guardar un cambio válido, crea la nueva versión, registra historial y responde 200 con valor+versión.
- **CA2:** reintento con la misma Idempotency-Key → devuelve el resultado anterior sin crear otra versión.
- **CA3:** PROFESOR intenta modificar → 403 y no cambia nada.
- **CA4:** valor fuera de rango → 400/422 con mensaje claro.

> **Alcance MVP:** el cambio aplica **de inmediato** (sin fecha de vigencia futura).

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Tests unitarios verdes.
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] sdd/docs actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-01 · T1 — [BACKEND] Migración Flyway y entidad GlobalParameter
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-01 · T2 — [BACKEND] Caso de uso UpdateParameterCommand
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-01 · T7 — [TEST] Tests unitarios de dominio
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-01 · T10 — [REVISION] Peer review + DoD Nivel 1
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

