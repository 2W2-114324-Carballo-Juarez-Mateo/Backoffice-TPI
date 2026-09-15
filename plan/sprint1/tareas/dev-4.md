# Dev 4 (Joaquin Cortez) — Tareas Sprint 1

> **Total:** 22h · **Código (BE+TEST):** 18h · **Capacidad real:** 35,2h (5h/día · 2 ausencias · 88%)
> **Primera tarea a arrancar (Day 1):** **T9** — contrato OpenAPI + diagrama de secuencia (arranca mientras Dev 3 hace la entidad).
> **Rama/PR:** `feature/us-01-parametros` (PR a develop #4, merge Día 7).
> **Depende de:** US-02 T1a (tabla `outbox_message`, merge Día 1 — Dev 1).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): configuración **solo hacia adelante** (RF-CFG-06), ADMIN-only, Outbox en la misma tx.
- **SKILLS.md**: `SKILL-endpoint`, `SKILL-build-test`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/05-endpoints.md`.
- **Parámetros:** `plan/PARAMETROS.md`.

---

## US-01 · Modificación y versionado de parámetros globales

### T3 — [BACKEND] Endpoints REST GET/PUT de parámetros con autorización por rol · 6h
**Descripción (plan/tareas.md):** Controladores GET y PUT con DTOs, @Valid, ADMIN escribe y PROFESOR solo lee (CA3).
**Qué hacer:** controladores GET y PUT con DTOs, `@Valid` y autorización (ADMIN escribe, PROFESOR solo lee).
**CA relacionados:** CA1, CA3.

### T4 — [BACKEND] Persistir el registro en `outbox_message` dentro de la misma transacción · 6h
**Descripción (plan/tareas.md):** Insertar OutboxMessage con payload GlobalConfigurationChanged en la misma tx (CA1). Requiere tabla de US-02.
**Qué hacer:** insertar el `OutboxMessage` (payload `GlobalConfigurationChanged`) en la **misma tx** del cambio de parámetro.
**CA relacionados:** CA1 (alimenta US-02).

### T8 — [TEST] Tests de integración con Testcontainers (PostgreSQL) · 6h
**Descripción (plan/tareas.md):** Persistencia real de parámetro + historial + outbox. PROFESOR → 403 (CA3), ADMIN → 200 (CA1).
**Qué hacer:** persistencia real de parámetro + historial + outbox; PROFESOR → 403 (CA3), ADMIN → 200 (CA1).
**CA relacionados:** CA1, CA3.

### T9 — [DOCUMENTACION] Congelar contrato OpenAPI 3 y diagrama de secuencia · 4h
**Descripción (plan/tareas.md):** Spec springdoc de los 3 endpoints. Diagrama ADMIN → Gateway → Service → Outbox. Actualizar sdd/backend/docs/05.
**Qué hacer:** spec springdoc de los endpoints + diagrama ADMIN → Gateway → Service → Outbox. Actualizar `sdd/backend/docs/05`.

## Criterios de aceptación (US-01)
- **CA1:** al guardar un cambio válido, crea la nueva versión, registra historial y responde 200 con valor+versión.
- **CA2:** reintento con la misma Idempotency-Key → devuelve el resultado anterior sin crear otra versión.
- **CA3:** PROFESOR intenta modificar → 403 y no cambia nada.
- **CA4:** valor fuera de rango → 400/422 con mensaje claro.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Tests de integración verdes (Testcontainers).
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] OpenAPI/sdd actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-01 · T3 — [BACKEND] Endpoints REST GET/PUT de parámetros
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-01 · T4 — [BACKEND] Persistir en outbox_message (misma tx)
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-01 · T8 — [TEST] Integración con Testcontainers
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-01 · T9 — [DOCUMENTACION] Contrato OpenAPI 3 + diagrama de secuencia
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

