# Dev 4 — Tareas Sprint 1

> **Total:** 22h · **Código (BE+TEST):** 18h
> **Rama/PR:** `feature/us-01-parametros` (PR #4, merge Día 7).
> **Depende de:** US-02 T1a (tabla `outbox_events`, merge Día 1 — Dev 1).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): configuración **solo hacia adelante** (RF-CFG-06), ADMIN-only, Outbox en la misma tx.
- **SKILLS.md**: `SKILL-endpoint`, `SKILL-build-test`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/05-endpoints.md`.
- **Parámetros:** `plan/PARAMETROS.md`.

---

## US-01 · Modificación y versionado de parámetros globales

### T3 — [BACKEND] Endpoints REST GET/PUT de parámetros con autorización por rol · 6h
**Qué hacer:** controladores GET y PUT con DTOs, `@Valid` y autorización (ADMIN escribe, PROFESOR solo lee).
**CA relacionados:** CA1, CA3.

### T4 — [BACKEND] Persistir el registro en `outbox_events` dentro de la misma transacción · 6h
**Qué hacer:** insertar el `OutboxMessage` (payload `GlobalConfigurationChanged`) en la **misma tx** del cambio de parámetro.
**CA relacionados:** CA1 (alimenta US-02).

### T8 — [TEST] Tests de integración con Testcontainers (PostgreSQL) · 6h
**Qué hacer:** persistencia real de parámetro + historial + outbox; PROFESOR → 403 (CA3), ADMIN → 200 (CA1).
**CA relacionados:** CA1, CA3.

### T9 — [DOCUMENTACION] Congelar contrato OpenAPI 3 y diagrama de secuencia · 4h
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