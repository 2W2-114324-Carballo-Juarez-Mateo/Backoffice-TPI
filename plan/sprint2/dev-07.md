# dev-07.md (Cerasulo, Regina) — Tareas Sprint 2

> **Disponibilidad:** media · **Núcleo:** 12 u · **Con gate:** 2 u · **Stretch:** 5 u · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| 04-T2 | Cliente real de proveedores y credenciales (providers, provider-credentials, discover-models, test-model) sobre 04-T1. La key viaja a T07; **nunca** se loguea ni se devuelve | BACK | M | CP3 |
| 04-T3 | Pantalla 09 conectada a `/api/backoffice/llm/...` | FRONT | M | CP3 |
| 04-T6 | OpenAPI de la fachada de proveedores y modelos | DOC | S | CP3 |
| #310 | `GET /api/backoffice/reports/courses/{courseId}/teacher`: alumnos con semáforo y factores, promedio **solo del propio curso**, `DataFreshnessDto`, < 2 s. Usa `ReportScopeResolver` (Máximo) y `CohortSummaryQuery` (Damián); **el adaptador de T02 ya no es tuyo** (pasó a #311) | BACK | M | CP3 |
| #306 | Partición de equivalencia y valores límite del riesgo (10/11 días, 4/5 días, 39/40 %, 60/61 %, 69/70 %) + las decisiones R-1/R-2 que tome el grupo | TEST | M | CP4 |
| 10-M2 | **Redefinida:** tests del proveedor de frescura (14/15/16 min, fuente sin eventos, PAR-23 modificado) **y del proyector #305** (idempotencia, reproceso, checkpoint) | TEST | M | CP4 |
| 15-T11 | Peer review del builder de US-15 | REV | S | CP5 |

## Con gate

| ID | Tarea | Capa | Tamaño | Gate |
|---|---|---|---|---|
| #1657 | Estado de 2FA y sesión | FRONT | S | T01 |
| 06-T7 | Peer review de la fachada de calibración | REV | S | C1 |

## Stretch (solo con tu núcleo mergeado) — HU14 vertical

| ID | Tarea | Capa | Tamaño |
|---|---|---|---|
| #326/#330 | `V23__reporting_alerts.sql` (`alert_threshold` + `alert`, con RLS si hay `course_id`), CRUD solo ADMIN, evaluador periódico sobre los indicadores de HU11/HU13 y **alertas internas** (activa/resuelta). **No se emite `THRESHOLD_BREACHED`**: no está en el catálogo de T11 | BACK | L |
| 14-T3 | Panel de umbrales + lista de alertas activas | FRONT | M |

## Revisiones que te tocan

- **01-IT** (Mateo): test de integración del registro de parámetros.

## Fuera de tu lista (vs propuesta)

- **#320** (anonimato) pasa a Mateo: todo HU13 queda en un solo servicio con un dueño.

## Insumos para la wiki (Ana)

Pasale ejemplos reales de request/response de proveedores LLM y del panel docente, y revisá su sección antes de que la cierre.

## Archivos

- **Tuyos:** `services/llm/provider/impl/*` (cliente real), FE `09-llm-providers/*`, `reporting/controllers/panel/*` y su servicio, `V23` y alertas (stretch).
- **No los tocás:** `RestClient` de T07 y la capa de acceso (Máximo), read model y riesgo (Damián), frescura (Valentina).

## Dependencias

- **Dependés de:** 04-T1 (Máximo, CP2) · #311 (Máximo, CP2) · V18 (Damián, CP2) · S2-00.
- **Dependen de vos:** Damián (#313 consume tu endpoint; con `TeacherPanelResponseDto` congelado arranca con mocks).

## Te testean / revisan

04-T2/04-T3 → Luciano (04-T4) y Valentina (05-T7), revisa Bruno (04-T5) · #310 → Luciano (#314/#315), revisa Mateo (#317) · HU14 → Máximo (14-T4), revisa Joaquín (14-T6).

## Si en el CP2 no llegó T01 (#1657)

Pasa a "Necesita información" con el pedido documentado en `CONTRATOS_T01_SOLICITUD.md`.

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** CA de HU04 y HU12 (panel), RLS verificado, OpenAPI al día.
