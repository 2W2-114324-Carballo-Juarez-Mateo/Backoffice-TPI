# dev-03.md (Baigorria, Damián) — Tareas Sprint 2

> **Disponibilidad:** media (2 días de ausencia) · **Núcleo:** 15 u · **Con gate:** — · **Stretch:** 4 u · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| H-06 | PR #54 `release/v1.0.0 → main`: CI verde, revisión (Mateo), merge y tag `v1.0.0` (cierre formal del Sprint 1) | — | S | CP0 |
| C3 | **Rehacer la solicitud a T02** con el envelope de 6 campos y `courses.events` (la actual usa el de 8 campos y `course.events`). Pedir: pertenencia docente, `ROSTER_UPDATED`, encuestas **agregadas** con conteo 1–5 + abstenciones + dimensión + `courseClosed`, y `COURSE_CLOSED`. Explicar por qué no queremos respuestas individuales (RF-ENC-04) | DOC | S | CP0 |
| C5 | **Primera solicitud a T05** (entregas y resultados prácticos, PAR-19/20): hoy no existe. Con C3, completás las filas de **T02 y T05** en la tabla de firmas de `CONTRATOS.md` | DOC | S | CP0 |
| 07-T1 | Validar PAR-14 en `ParameterValueRules` con la forma oficial de Skill Hub (`backoffice-t07-evaluacion-llm-contract` v1): `{"average": 5, "dimension": 10}` (**clave `average`, no `promedio`**), ambas numéricas y `0 < average ≤ dimension ≤ 100`. **Incluye renombrar `promedio → average` en el seed (migración)** | BACK | S | CP2 |
| P-12 | **Confirmado por Skill Hub (`backoffice-t08-banco-contract` v1): PAR-12 es nuestro**, lo consume T08 (Banco). Seed en `V24` (`{"initialLives": 3, "maxLives": 3}`), regla `1 ≤ initialLives ≤ maxLives` y corregir `AGENTS.md` (ya hecho en PR #56). Además: **sembrar PAR-03/06/07/24** (también son nuestros según Skill Hub) en la misma migración o una contigua | BACK | S | CP2 |
| #303 | `V18__reporting_cohort_read_model.sql` (`cohort_roster`, `student_activity_summary` con columnas de riesgo e índices por `course_id`) + entidades + implementación de `CohortSummaryQuery` | BACK | M | **CP2** |
| #304 | Clasificador de riesgo **puro** (sin Spring) + recálculo. Umbrales en `@ConfigurationProperties("reporting.risk")`; "vidas agotadas" detrás de flag. Aplicar las decisiones R-1 y R-2 que tome el grupo | BACK | M | CP3 |
| #312 | `STUDENT_AT_HIGH_RISK` por `DomainEventOutbox` **solo en la transición** a RED, en la misma transacción que el recálculo, a `notifications.events` (payload con IDs, sin PII) | BACK | S | CP3 |
| #313 | Panel docente FE con semáforo (color **y** texto, teclado, WCAG AA) y badge de frescura, contra `TeacherPanelResponseDto` | FRONT | M | CP4 |
| 15-T9 | Specs del builder de US-15 (crear/editar/correr plantilla, validaciones, 403) | TEST | M | CP5 |
| #325 | Peer review de privacidad de HU13 | REV | S | CP4 |

## Stretch (solo con tu núcleo mergeado)

| ID | Tarea | Capa | Tamaño |
|---|---|---|---|
| 09-T1 | HU09: `V25__reporting_export_job.sql` + `POST /reports/exports` → job asíncrono → CSV → `EXPORT_READY` por outbox → descarga. Hereda scope, anti-comparación y anonimato | BACK | L |
| 09-T2 | Conectar las export tools de la slice 07 (las implementaste en el S1, `ff33671`) al export asíncrono | FRONT | S |

## Insumos para la wiki (Ana)

Pasale las tablas de parámetros y del read model para el DER, ejemplos reales de request/response de parámetros y los estados del riesgo (`RED`/`YELLOW`/`GREEN`), y revisá su sección antes de que la cierre.

## Archivos

- **Tuyos:** `ParameterValueRules` (PAR-14/PAR-12), `V18`, `V24`, `V25`, `reporting/entities` del read model y del riesgo, `reporting/services/risk/*`, FE panel docente.
- **No los tocás:** el proyector que llena tus tablas (#305, Valentina) ni el endpoint del panel (#310, Regina). Tu contrato con ellas es `CohortSummaryQuery` y el esquema de `V18`: si hay que cambiarlo después del CP2, **se avisa en el canal y se hace con PR propia**.

## Dependencias

- **Dependen de vos:** Valentina (#305 escribe en V18), Regina (#310 lee V18), Máximo (V19 aplica RLS sobre V18). Por eso **V18 va en el CP2**.
- **Dependés de:** S2-00 (`RiskLevel`, `CohortSummaryQuery`) · `DomainEventOutbox` (ya en `develop`).

## Te testean / revisan

#303/#304 → Regina (#306), revisa Máximo (#308) · #312 → Luciano (#315), revisa Mateo (#317) · #313 → Joaquín (12-T9), revisa Mateo · 07-T1/P-12 → Máximo (07-T4, parte PAR-14), revisa Mateo · HU09 → Bruno o Joaquín (09-T3), revisa Luciano.

## Si en el CP2 no llegó T02 (C3)

El padrón se deriva de los alumnos que aparecen en eventos de T03 y el panel muestra "padrón no disponible". Queda registrado como limitación en #307.

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** CA de HU11/HU12, RLS verificado, docs al día.
