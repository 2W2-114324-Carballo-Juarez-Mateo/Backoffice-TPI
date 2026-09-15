# Dev 7 (Maximo Cerquatti) — Tareas Sprint 1

> **Total:** 21h · **Código (BE+TEST):** 18h · **Capacidad real:** 32,4h (6h/día · 4 ausencias · 90%)
> **Primera tarea a arrancar (Day 1):** **US-03 T1** — filtro de seguridad/headers (sin BD).
> **Ramas/PRs:** `feature/us-08-ingesta-kafka` (PR #3) · `feature/us-03-gateway-auth` (PR #2).

## Cómo trabajar (obligatorio)
- **RULES.md** (raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`): validar ≠ autorizar, roles reales (`ADMIN`/`PROFESOR`), idempotencia.
- **SKILLS.md**: `SKILL-evento`, `SKILL-build-test`.
- **DoD** (`plan/sprint0/Sprint0-Propuesta.md` §2): Nivel 0 y Nivel 1.
- **SDD:** `plan/sdd/backend/docs/07-seguridad.md`, `08-eventos-kafka.md`.
- **Contratos:** `plan/CONTRATOS.md` (headers del gateway de T01).

---

## US-08 · Ingesta de datos de los temas con deduplicación

### T5 — [TEST] Integración: ingesta, deduplicación y DLT · 8h
**Descripción (plan/tareas.md):** Testcontainers (Kafka + PostgreSQL). BDD Esc. 1 (nuevo → procesado), BDD Esc. 2 (duplicado → ignorado), BDD Esc. 3 (malformado → DLT).
**Qué hacer:** Testcontainers (Kafka + PostgreSQL): nuevo → procesado (CA1); duplicado → ignorado (CA2); malformado → DLT (CA3).
**CA relacionados:** CA1, CA2, CA3.

### T7 — [REVISION] Peer review de consumidores y control en Taiga · 3h
**Descripción (plan/tareas.md):** Asignación de particiones, commit de offsets, tolerancia a fallos.
**Qué hacer:** revisar asignación de particiones, commit de offsets y tolerancia a fallos.

## US-03 · Consola de gestión administrativa (integración de identidades)

### T1 — [BACKEND] Filtro de seguridad e inspección de headers del Gateway · 4h
**Descripción (plan/tareas.md):** Validación de tokens y extracción de contexto de autorización en administration-service sin persistencia local de usuarios.
**Qué hacer:** filtro que extrae el contexto de autorización de los headers propagados (`X-User-Roles`, `X-User-Id`, `X-Principal-Type`), **sin persistir usuarios**.
**CA relacionados:** CA1 (autorización por rol).
**Refs:** `plan/CONTRATOS.md` (contratos T01 cerrados).

### T5 — [TEST] Integración del filtro de seguridad y autorización por headers · 6h
**Descripción (plan/tareas.md):** MockMvc: validar que requests con X-User-Roles: ADMIN acceden a administración y PROFESOR recibe 403.
**Qué hacer:** MockMvc: `X-User-Roles: ADMIN` accede a administración y `PROFESOR` recibe 403.
**CA relacionados:** CA1, CA3.

## Criterios de aceptación (US-03)
- **CA1:** asignar rol a persona existente → activa, registra auditoría y responde 201.
- **CA2:** un admin no puede quitarse el rol en sesión activa → 400.
- **CA3:** quitar el rol al último admin → 409.
- **CA4:** toda baja exitosa publica el aviso con el número de admins restantes.

## Criterios de aceptación (US-08)
- **CA1/CA2/CA3:** nuevo se procesa, repetido se descarta, malformado va a DLT.

## DoD (Nivel 0 — Tarea) — checklist
- [ ] Compila y pasa lint/estilo.
- [ ] Tests verdes (Testcontainers / MockMvc).
- [ ] Sin secretos ni hardcodes; cumple RULES.md.
- [ ] PR con ≥1 review aprobado.
- [ ] sdd/docs actualizados.

---

## Registro de trabajo / trazabilidad (feedback de la IA)

> Completar al terminar cada tarea, para dejar constancia de qué se hizo y cómo. Formato definido en el [README](README.md#registro-de-trabajo--trazabilidad-feedback-de-la-ia).

### US-08 · T5 — [TEST] Integración: ingesta, deduplicación y DLT
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-08 · T7 — [REVISION] Peer review de consumidores
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-03 · T1 — [BACKEND] Filtro de seguridad e inspección de headers
- **Estado:** ⬜ pendiente / 🟡 en curso / ✅ hecho
- **Qué se hizo:**
- **Archivos/clases tocadas:**
- **Decisiones / supuestos:**
- **CA / RF cubiertos:**
- **Tests agregados:**
- **PR / commits:**
- **Pendientes / deuda técnica:**

### US-03 · T5 — [TEST] Integración del filtro de seguridad
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

