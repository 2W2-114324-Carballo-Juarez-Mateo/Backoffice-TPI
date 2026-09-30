# dev-03.md (Baigorria, Damián) — Tareas Sprint 2

> **Núcleo:** 3.020 líneas · **Condicionado y extra:** 1.000 · **Total techo:** 4.020 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

## Núcleo

| ID | Tarea | Capa | Líneas | CP |
|---|---|---|---:|---|
| #303 | Read model de cohorte: **`V19__reporting_cohort_read_model.sql`** (`cohort_roster`, `student_activity_summary` con columnas de riesgo e índices por `course_id`) + entidades + `CohortSummaryQuery`. **No V18: ya lo usa la release** | BACK | 420 | **CP2** |
| #304 | Clasificador de riesgo **puro** (sin Spring) + recálculo. Umbrales en `reporting.risk.*`. Reglas decididas: **R-1** el hueco va a YELLOW · **R-2** las tasas solo con ≥ 3 intentos (ver §6.1 de `tareas-sprint2.md`) | BACK | 760 | CP3 |
| #312 | `STUDENT_AT_HIGH_RISK` por `DomainEventOutbox` **solo en la transición** a RED, misma transacción que el recálculo, a `notifications.events` (payload con IDs, sin PII) | BACK | 240 | CP3 |
| 15-T8 | **Builder de reportes de US-15** (FE): métricas, filtros, período, columnas, agrupación, "Guardar plantilla", WCAG AA; solo ofrece las métricas de `GET /reports/metrics` (pasó de Luciano a vos) | FRONT | 1.400 | CP5 |
| #3331 | Solo lectura de parámetros para PROFESSOR (FE); el backend ya lo permite (pasó de Bruno a vos) | FRONT | 200 | CP3 |
| | **Subtotal núcleo** | | **3.020** | |

## Condicionado y extra

| ID | Tarea | Capa | Líneas | Condición |
|---|---|---|---:|---|
| 09-T1 | HU09: `V25__reporting_export_job.sql` + `POST /reports/exports` → job asíncrono → CSV → `EXPORT_READY` por outbox → descarga. Hereda scope, anti-comparación y anonimato; el CSV se protege contra inyección de fórmulas | BACK | 1.000 | Extra (solo con el núcleo mergeado) |
| | **Subtotal** | | **1.000** | |

## Sin líneas de código (documentación y revisión)

- **H-06:** PR #54 `release/v1.0.0 → main` (CI verde, revisión, merge y tag `v1.0.0`) si todavía sigue abierta.
- **C3:** rehacer la solicitud a T02 (envelope de 6 campos, `courses.events`, distribución 1–5, abstenciones, dimensión, `courseClosed`, pertenencia) y fila de T02. **C5:** primera solicitud a T05 y fila de T05.
- **Revisás:** **la PR #56 de Mateo** (vos sos el dueño original del registro de parámetros) · #325 (privacidad de HU13 y #322) · 07-T6 (calibración).
- **Insumos para la wiki (Ana):** tablas de parámetros y read model para el DER, ejemplos de parámetros y estados del riesgo (`RED`/`YELLOW`/`GREEN`).

## Archivos

- **Tuyos:** `V19`, `V25`, `reporting/entities` del read model y del riesgo, `reporting/services/risk/*`, FE builder de reportes, FE solo lectura de parámetros.
- **No los tocás:** `ParameterValueRules` y `V24` (PR #56 de Mateo), el proyector que llena tus tablas (#305, Valentina), el endpoint del panel (#310, Regina). Si tu esquema cambia después del CP2, se avisa y va en PR propia.

## Dependencias

- **Dependen de vos:** Valentina (#305 escribe en V19), Regina (#310 lee V19), Máximo (V20 aplica RLS sobre V19): por eso **V19 va en el CP2**.
- **Dependés de:** S2-00 · catálogo de Joaquín (15-T1) y motor de Bruno (15-T2/T4) para probar el builder con datos reales: arrancás contra el contrato congelado.

## Te testean / revisan

#303 y #304 → Regina (#306), revisa Máximo (#308) · #312 → Luciano (#315), revisa Mateo (#317) · 15-T8 → specs Bruno (15-T9), revisa Regina (15-T11) · #3331 → specs Mateo (05-N3), revisa Joaquín (05-N4) · HU09 → tests Máximo, revisa Luciano.

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · OpenAPI y `docs/` al día en la misma PR.
