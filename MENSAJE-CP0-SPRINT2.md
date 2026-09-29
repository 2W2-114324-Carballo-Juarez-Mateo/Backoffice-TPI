# Sprint 2 · CP0 — Confirmación y arranque (mensaje para el grupo)

> **Paquete:** `sprint2 (2)` — plan corregido y auditado. Para tomarlo como DEFINITIVO solo faltan las 3 decisiones de abajo y el arranque de CP0.
> **Actualización contratos (Skill Hub):** la mayoría de los contratos ya están publicados en el Skill Hub (los listo en §2). Por eso los C-tasks son en su mayoría **leer + firmar** (no "enviar solicitud" a ciegas); solo T02 y T10 siguen sin publicar.

---

## 1 · Decisiones del grupo (CONFIRMADAS — elegidas las recomendadas)

### R-1 · Un alumno en la "zona gris" del semáforo → ¿qué color le toca?

**El contexto.** El semáforo de riesgo (HU11) dice:
- 🔴 **ROJO** si lleva **más de 10 días** sin actividad, o reprueba **más del 60 %** de sus intentos.
- 🟡 **AMARILLO** si lleva **5–10 días** sin actividad, o reprueba entre **40 y 60 %**.
- 🟢 **VERDE** si aprueba **70 % o más**.

**El problema.** Hay un caso que **no encaja en ninguna categoría**: un alumno que aprueba entre el **60 % y el 70 %** y está **activo** (entra seguido). No es VERDE (no llega a 70 %), no es AMARILLO por inactividad (está activo), y no es ROJO por reprobación (reprueba menos del 40 %). La regla "no sabe" qué hacer con él.

> ✅ **DECIDIDO: 🟡 AMARILLO** (A — preferimos avisar de más).

---

### R-2 · ¿Cuántos intentos hacen falta para "sacar cuentas"?

**El contexto.** Para calcular "reprobación" o "aprobación" necesitamos varios intentos, si no la estadística miente. Ejemplo: un alumno con **1 solo intento reprobado** tendría "100 % de reprobación" → lo mandaríamos a 🔴 ROJO, cuando en realidad solo intentó una vez y puede que sea nuevo en la plataforma.

**El problema.** ¿Con cuántos intentos empezamos a calcular las tasas? Con menos, el semáforo sale **solo de la inactividad** (y se muestra "muestra insuficiente" en el panel).

> ✅ **DECIDIDO: mínimo 3 intentos** (A). Con 1 o 2 intentos no se calculan tasas; el estado sale de la inactividad.

---

### P-12 · PAR-12 (vidas iniciales y máximas) → ¿de quién es?

**El contexto.** PAR-12 define **cuántas vidas arranca un alumno y cuántas puede tener** (`{"initialLives": 3, "maxLives": 3}`).

**El problema.** Teníamos una **contradicción interna** entre `PARAMETROS.md` (CONFIRMADO del Backoffice), `AGENTS.md`/`V2` (EXTERNO de T08) y nuestro propio contrato publicado en Skill Hub.

> ✅ **DECIDIDO: es del Backoffice** (A) — lo confirma el Skill Hub (`backoffice-t08-banco-contract` v1: T08 lo consume de nosotros). Se siembra en **V24** y se corrige `AGENTS.md` (ya en PR #56). **Extensión (mismo criterio): PAR-03/06/07/24 también son nuestros** → sembrarlos.

---

## 2 · Contratos — qué ya está en Skill Hub y qué falta

> **Conclusión:** los contratos **ya están en Skill Hub** en su mayoría. Los C-tasks cambian de "enviar solicitud" a **leer el Skill Hub → registrar en `CONTRATOS.md` + firmar la fila** (en la misma PR). Solo T02 y T10 siguen sin publicar.

| ID | Tema | Estado en Skill Hub | Acción real en CP0 |
|---|---|---|---|
| **C8** | **T03** · `CHALLENGE_COMPLETED` | ✅ `challenge-engine-kafka-events` (v1): **payload completo** · `result.status` = `APPROVED` / `DISAPPROVE` · topic `challenges.events` (no existe `challenges.results`) | **Ya respondido.** Valentina lee el contrato, ajusta el proyector y firma (no manda solicitud) |
| **C1** | **T07** · `/api/llm/admin/*` | ✅ `llm-service-http-contract` (v8) + `backoffice-admin-and-llm-service-integration-contract` (v2) + `llm-service-kafka-contract` (v13) | Máximo lee, corrige el §6 como fachada y firma T07/T01 |
| **C2** | **T11** · `notifications.events` | ✅ `contratos-kafka` (v5) ratifica topics/envelope | Mateo confirma **materialización** + payload `STUDENT_AT_HIGH_RISK`/`EXPORT_READY` + si registran `THRESHOLD_BREACHED` |
| **C9** | **T08** · `accounting.events` | ✅ `accounting-service-kafka-contract` (v1) | Bruno firma la fila T08 (acuerdo parcial ya escrito) |
| **C5** | **T05** · entregas | 🟡 **parcial**: T05 publica en el mismo `challenges.events` (lo dice el contrato de T03: "Theme 05 also publishes to this topic") | Damián confirma si T05 usa el mismo `result.status` + PAR-19/20 (solicitud chica) |
| **C3** | **T02** · roster / CSAT / pertenencia | ❌ **no publicado** (no hay contrato de lectura de Cursos) | Damián hace la solicitud (envelope 6 campos, `courses.events`, encuestas agregadas, pertenencia) |
| **C6** | **T10** · progreso / XP / vidas | ❌ **no publicado** (solo está el contrato de PAR-08/09, no el de lectura) | Luciano hace el seguimiento |

### ⚠️ Dos ajustes que descubrí leyendo el Skill Hub (para corregir el plan)
1. **Métrica "Uso del tutor IA" del catálogo US-15 (fuente `usoTutorIa`/`cantidadConsultasIa`):** el contrato real de T03 (`challenge-engine-kafka-events`) **NO trae esos campos**. Hay que **corregir el catálogo** (quitar esa métrica o marcarla no-disponible) — no se puede leer lo que el evento no lleva.
2. **T03 está "implementation in progress"** (G09 sigue publicando el formato viejo hasta que anuncie): el proyector #305 se construye contra el contrato, pero **no habrá datos vivos hasta que G09 lo anuncie** (no es bloqueante para desarrollar).

---

## 3 · Cada dev valida su `dev-XX.md` (5 puntos)

- [ ] Todas mis tareas son del Tema 12 (ninguna de otro grupo).
- [ ] Ningún archivo que voy a tocar es de otro dev (§5, §7 y la sección "Archivos").
- [ ] Mis dependencias están en **S2-00** o tienen un gate con fallback.
- [ ] Sé qué CA cubro y quién me testea y revisa (matriz §9).
- [ ] Si una tarea mía tiene gate, sé qué hago en el CP2 si no llega el contrato.

---

## 4 · Acciones de arranque

- **Ana (MSII):** corregir Taiga (estados incoherentes, reasignar **#284 a Valentina**, renombrar #3537/#3539, cargar HT08/HU01-bis/HU10-bis/US-15) + crear el esqueleto de la **wiki de G06**.
- **Abrir PRs de ramas ya terminadas:** #3512 guards (`8c2c82e`, Máximo) y #291 badge (Valentina).
- **Release del S1:** PR #54 (`release/v1.0.0 → main`) ya tiene 3 approves + CI verde → falta **merge + tag `v1.0.0`**.
- **S2-00 (Luciano):** PR de contratos compartidos en el **CP1**, revisado por Máximo + Mateo.

---

## 5 · Confirmación (lo que queda)

Las **3 decisiones ya están tomadas** (R-1 = 🟡 AMARILLO · R-2 = mínimo 3 · P-12 = Backoffice). Lo que falta para dejar el plan cerrado:
- Cada dev **valida su `dev-XX.md`** (checklist del punto 3) y responde ✅ en el canal.
- Enviar los **contratos C1, C2, C3, C5, C6, C8, C9** (§2).
- Ana corrige **Taiga** y crea el esqueleto de la **wiki de G06**.

Con eso el plan queda **definitivo** y arranca el Sprint 2.