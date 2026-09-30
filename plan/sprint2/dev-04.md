# dev-04.md (Cortez, Joaquín) — Tareas Sprint 2

> **Núcleo:** 3.000 líneas · **Condicionado y extra:** 1.230 · **Total techo:** 4.230 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

> **Etiquetas de tipo de trabajo** (para cargar en Taiga, una o más por tarea): Backend · Frontend · Testing · Base de Datos · DevOps · Documentación · Análisis · Diseño / UX-UI · Integración · Configuración · Seguridad · Investigación · Gestión · Otro. Criterio completo e índice maestro en `etiquetas-tareas.md`.

## Núcleo

| ID | Tarea | Capa | Líneas | CP | Etiquetas |
|---|---|---|---:|---|---|
| 15-T1 | Catálogo de métricas de US-15 (lista blanca por enum) con fuente y disponibilidad según el contrato; `GET /reports/metrics` con su OpenAPI. **Sin `usoTutorIa`**: T03 no lo envía | BACK | 550 | CP3 | Backend, Análisis |
| 15-T3 | Plantillas y favoritas, solo del dueño: **`V21__reporting_report_template.sql`** (`owner_id, course_id, config jsonb, is_favorite`). El `config` se valida contra la lista blanca al guardar; OpenAPI incluida | BACK | 850 | CP3 | Backend, Base de Datos |
| HU13 | **KPIs CSAT completos (pasó de Mateo a vos):** `V22__reporting_survey_summary.sql` (conteos por estrella, abstenciones, dimensión, `course_closed`, **sin autor ni timestamp preciso**, con política RLS) · KPI-01/02 = % 4–5 y % 1–2 sobre respuestas emitidas · abstenciones aparte (RF-ENC-10) · **PAR-18 y curso cerrado** para el PROFESOR (RF-ENC-13) · `GET /reports/courses/{courseId}/kpis` y `GET /reports/platform` (solo ADMIN, desglose por curso **sin ranking**) · `DataFreshnessDto` | BACK | 1.600 | CP4 | Backend, Base de Datos, Seguridad |
| | **Subtotal núcleo** | | **3.000** | | |

## Condicionado (gate C1: T07 confirma `/api/llm/admin/*` en el CP2)

| ID | Tarea | Capa | Líneas | Etiquetas |
|---|---|---|---:|---|
| #3537 | Fachada del perfil de calibración institucional (golden set y rúbrica) sobre T07 | BACK | 480 | Backend, Integración |
| #3538 | Pantalla del perfil de calibración (parte 12) | FRONT | 750 | Frontend, Integración |
| | **Subtotal** | | **1.230** | |

## Sin líneas de código (documentación y revisión)

- **#324:** políticas de privacidad y fórmulas de los KPIs. **06-T8:** OpenAPI de la fachada de calibración (en el código). *Etiquetas: Documentación, Seguridad.*
- **Revisás:** 05-T6 (fachada de modelos de Mateo) · 05-N4 (guards, migración de rutas, badge, 2FA y solo lectura) · 14-T6 (alertas, si se hace el extra). *Etiquetas: Testing.*
- **Insumos para la wiki (Ana):** tabla de contratos para el DER, ejemplos de métricas, plantillas y KPIs.

## Archivos

- **Tuyos:** `V21`, `V22`, catálogo y plantillas de US-15, `reporting/**/kpi/*`, fachada del perfil de calibración.
- **No los tocás:** el motor y el `run` (Bruno). Tu contrato con él es el enum de métricas y dimensiones de S2-00: si falta una métrica, **se agrega a tu catálogo y le avisás**.

## Dependencias

- **Dependen de vos:** Bruno (el motor usa tu catálogo), Damián (el builder lista tus métricas y plantillas), Mateo (#322 consume tus KPIs).
- **Dependés de:** S2-00 (enums y DTOs) · `ReportScopeResolver` de Máximo (CP2) · C3 de Damián (sin datos de T02 los KPIs responden "sin datos").

## Te testean / revisan

15-T1 y 15-T3 → Máximo (15-T5), revisa Valentina (15-T7) · HU13 → Regina (#323), revisa Damián (#325) · #3537 y #3538 → Máximo (06-T5), revisa Regina (06-T7).

## Si en el CP2 no llegó C1 (T07)

#3537/#3538 quedan con stub + flag y pasan a "Necesita información"; no bajan tu núcleo, que ya es el objetivo del reparto.

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · OpenAPI y `docs/` al día en la misma PR.
