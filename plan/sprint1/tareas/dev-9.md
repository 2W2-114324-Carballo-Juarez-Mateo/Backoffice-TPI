# Dev 9 — Tareas Sprint 1

> **Total:** 22h · **Código (BE+TEST):** 16h
> **Rama/PR:** `feature/us-04-modelos-ia` (PR #5, merge Día 8).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): sin secretos (API Keys cifradas/enmascaradas), ADMIN-only.
- **SKILLS.md**: `SKILL-endpoint`, `SKILL-caso-uso`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/05-endpoints.md`, `07-seguridad.md`.

---

## US-04 · Registro de proveedores y modelos de IA

### T2 — [BACKEND] Endpoint de registro de proveedores y catálogo con enmascaramiento · 6h
**Qué hacer:** `POST` de registro (→ 201) y `GET` de catálogo con estado; claves enmascaradas `sk-****`.
**CA relacionados:** CA1, CA2, CA3.

### T5 — [TEST] Seguridad: cifrado en BD y enmascaramiento en respuestas · 6h
**Qué hacer:** verificar que la API Key persiste **cifrada** y que ningún endpoint la devuelve en texto plano.
**CA relacionados:** CA3.

### T6 — [TEST] Validar bloqueo de activación para modelos en `PENDING_REVIEW` · 4h
**Qué hacer:** intentar activar un modelo recién registrado → rechazo.
**CA relacionados:** CA1.

### T7 — [DOCUMENTACION] Documentar endpoints en OpenAPI y actualizar SDD · 3h
**Qué hacer:** spec springdoc + máquina de estados del modelo.

### T8 — [REVISION] Peer review de seguridad de credenciales y control en Taiga · 3h
**Qué hacer:** que no se filtren API Keys en logs, respuestas ni repo.

## Criterios de aceptación (US-04)
- **CA1:** registrar un modelo válido → 201, queda "pendiente de revisión" y no se puede activar.
- **CA2:** el catálogo muestra el estado de cada modelo.
- **CA3:** las claves nunca aparecen completas en ninguna respuesta.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Tests de seguridad verdes.
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] OpenAPI/sdd actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-04 · T2 — [BACKEND] Endpoint de registro y catálogo con enmascaramiento
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-04 · T5 — [TEST] Seguridad: cifrado en BD y enmascaramiento
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-04 · T6 — [TEST] Bloqueo de activación en PENDING_REVIEW
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-04 · T7 — [DOCUMENTACION] OpenAPI de proveedores/modelos
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-04 · T8 — [REVISION] Peer review de seguridad de credenciales
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

