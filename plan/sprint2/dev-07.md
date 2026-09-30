# dev-07.md (Cerasulo, Regina) — Tareas Sprint 2

> **Núcleo:** 2.990 líneas · **Condicionado y extra:** 1.000 · **Total techo:** 3.990 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

## Núcleo

| ID | Tarea | Capa | Líneas | CP |
|---|---|---|---:|---|
| 04-T2 | Cliente real de proveedores y credenciales (providers, provider-credentials, discover-models, test-model) sobre 04-T1. La key viaja a T07; **nunca** se loguea ni se devuelve | BACK | 660 | CP3 |
| 04-T3 | Pantalla 09 conectada a `/api/backoffice/llm/...` | FRONT | 350 | CP3 |
| #310 | `GET /api/backoffice/reports/courses/{courseId}/teacher`: alumnos con semáforo y factores, promedio **solo del propio curso**, `DataFreshnessDto`, < 2 s. Usa `ReportScopeResolver` (Máximo) y `CohortSummaryQuery` (Damián) | BACK | 700 | CP3 |
| #306 | 12 casos de riesgo: límites (10/11 y 4/5 días, 39/40, 60/61 y 69/70 %) más R-1, R-2 y los 3 escenarios de Taiga (tabla en §6.1 de `tareas-sprint2.md`) | TEST | 300 | CP4 |
| 10-M2 | Tests del proveedor de frescura (14/15/16 min, fuente sin eventos, PAR-23 modificado) **y del proyector #305** (idempotencia, reproceso, checkpoint) | TEST | 400 | CP4 |
| #323 | **Tests de anonimato de HU13** (pasó de Joaquín a vos): 4, 5 y 6 respuestas con PAR-18 = 5; curso abierto → sin puntajes; no-ADMIN en `/platform` → 403 | TEST | 300 | CP4 |
| 06B-T2 | **IT del orden del outbox** (pasó de Máximo a vos): falla v1 y v2 no sale antes (Testcontainers con Kafka), sobre 06B-T1 de Luciano | TEST | 280 | CP4 |
| | **Subtotal núcleo** | | **2.990** | |

## Condicionado y extra

| ID | Tarea | Capa | Líneas | Condición |
|---|---|---|---:|---|
| HU14 | Alertas configurables (BE): `V23__reporting_alerts.sql` (`alert_threshold` + `alert`, con RLS si hay `course_id`), CRUD solo ADMIN, evaluador periódico sobre los indicadores de HU11/HU13, **alertas internas**. **No publica `THRESHOLD_BREACHED`**: T11 lo excluyó | BACK | 1.000 | Extra (solo con el núcleo mergeado) |
| | **Subtotal** | | **1.000** | |

## Sin líneas de código (documentación y revisión)

- **Revisás:** 15-T11 (builder de US-15) · 06-T7 (calibración) · 01-IT de Mateo.
- **Sale de tu lista:** #1657 (2FA) pasó a Luciano.
- **Insumos para la wiki (Ana):** ejemplos de proveedores LLM y del panel docente.

## Archivos

- **Tuyos:** `services/llm/provider/impl/*`, FE `09-llm-providers/*`, `reporting/controllers/panel/*` y su servicio, `V23` (extra), tests de riesgo, de frescura, de anonimato y del orden del outbox.
- **No los tocás:** `RestClient` de T07 y capa de acceso (Máximo), read model y riesgo (Damián), frescura (Valentina), KPIs (Joaquín).

## Dependencias

- **Dependés de:** 04-T1 y #311 (Máximo, CP2) · V19 (Damián, CP2) · #305 de Valentina para 10-M2 · 06B-T1 de Luciano para 06B-T2 · HU13 de Joaquín para #323 · S2-00.
- **Dependen de vos:** Luciano (#313 consume tu endpoint; con `TeacherPanelResponseDto` congelado arranca con mocks).

## Te testean / revisan

04-T2 y 04-T3 → Luciano (04-T4) y Valentina (05-T7), revisa Bruno (04-T5) · #310 → Luciano (#314/#315), revisa Mateo (#317) · HU14 → Máximo (14-T4), revisa Joaquín (14-T6).

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · RLS verificado donde aplica · OpenAPI y `docs/` al día en la misma PR.
