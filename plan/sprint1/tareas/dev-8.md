# Dev 8 — Tareas Sprint 1

> **Total:** 20h · **Código (BE+TEST):** 16h
> **Ramas/PRs:** `feature/us-03-gateway-auth` (PR #2) · `feature/us-04-modelos-ia` (PR #5).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): validar ≠ autorizar, sin secretos (API Keys cifradas/enmascaradas).
- **SKILLS.md**: `SKILL-endpoint`, `SKILL-caso-uso`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/05-endpoints.md`, `07-seguridad.md`.
- **Contratos:** `plan/CONTRATOS.md` (T01).

---

## US-03 · Consola de gestión administrativa (integración de identidades)

### T2 — [BACKEND] Cliente HTTP hacia Tema 01 vía Gateway · 6h
**Qué hacer:** cliente Feign/WebClient M2M hacia Tema 01 para operaciones administrativas y **auditoría delegada** (`GET /api/users/audit`), sin almacenar usuarios.
**Refs:** `plan/CONTRATOS.md` (rutas `/api/users/**`).

### T7 — [DOCUMENTACION] Matriz de delegación de identidades con Tema 01 · 4h
**Qué hacer:** documentar que T01 es el SSOT de identidades; mapear headers `X-Principal-Type`, `X-User-Id`, `X-User-Roles` y el flujo SPA → Gateway → T01.

## US-04 · Registro de proveedores y modelos de IA

### T1 — [BACKEND] Migración Flyway y entidades `ModelProvider` / `LlmModel` con cifrado de API Keys · 6h
**Qué hacer:** tablas con estado (`ACTIVE`/`RETIRED`/`PENDING_REVIEW`) y **cifrado simétrico** de las API Keys.
**CA relacionados:** CA1.

### T3 — [BACKEND] Regla de estado inicial `PENDING_REVIEW` con bloqueo de activación · 4h
**Qué hacer:** modelo nuevo **no activable**; transición `PENDING_REVIEW → APPROVED → ACTIVE/STANDBY`.
**CA relacionados:** CA1.

## Criterios de aceptación (US-04)
- **CA1:** registrar un modelo válido → 201, queda "pendiente de revisión" y no se puede activar.
- **CA2:** el catálogo muestra el estado de cada modelo.
- **CA3:** las claves nunca aparecen completas en ninguna respuesta.

## Criterios de aceptación (US-03)
- **CA1:** asignar rol a persona existente → activa, audita y 201.
- **CA2:** admin no puede quitarse su rol en sesión → 400.
- **CA3:** quitar el rol al último admin → 409.
- **CA4:** baja exitosa publica el aviso con el número de admins restantes.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] sdd/docs actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-03 · T2 — [BACKEND] Cliente HTTP hacia Tema 01 vía Gateway
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-03 · T7 — [DOCUMENTACION] Matriz de delegación de identidades
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-04 · T1 — [BACKEND] Migración y entidades ModelProvider / LlmModel
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-04 · T3 — [BACKEND] Regla PENDING_REVIEW con bloqueo de activación
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

