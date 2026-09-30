# dev-08.md (Gianoli, Bruno) — Tareas Sprint 2

> **Núcleo:** 2.800 líneas · **Condicionado y extra:** 960 · **Total techo:** 3.760 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

## Núcleo

| ID | Tarea | Capa | Líneas | CP |
|---|---|---|---:|---|
| 15-T2 + 15-T4 | **Ruta crítica de US-15:** motor de consultas dinámicas (filtros, período, columnas, agrupación) **y** `POST /api/backoffice/reports/run` (con `templateId` o `config`), con su OpenAPI. Ejecuta dentro de `ReportScopeResolver` + RLS. Invariantes: solo métricas y dimensiones del enum, parámetros bind, sin `TEACHER`, encuestas solo agregadas con PAR-18 + curso cerrado, `DataFreshnessDto`, paginado y período máximo | BACK | 2.100 | **CP4** |
| 15-T9 | **Specs del builder de US-15** (FE, de Damián): crear, editar y correr plantilla, validaciones, 403 | TEST | 450 | CP5 |
| 12-T9 | **Specs del panel docente** (FE, de Luciano) | TEST | 250 | CP4 |
| | **Subtotal núcleo** | | **2.800** | |

## Condicionado (gate C1: T07 confirma `/api/llm/admin/*` en el CP2)

| ID | Tarea | Capa | Líneas |
|---|---|---|---:|
| #3539 | Fachada de corridas de calibración (crear, listar, detalle; `maeFinal`, `maxIndividualError` y veredicto **de T07**) | BACK | 500 |
| 07-T2 | Estado de calibración del modelo activo (último veredicto y deriva, leídos de T07) | BACK | 460 |
| | **Subtotal** | | **960** |

## Sin líneas de código (documentación y revisión)

- **H-04:** `feature/mvp-s6-golden-set-runs` **no se mergea** (MAE y veredicto son de T07 por la Opción A; la PR #30 ya está cerrada). Tag `archive/s6-golden-set-local` y borrar la rama. `ToleranceEvaluator` de esa rama sirve de referencia para leer PAR-14.
- **#286:** documentar que T07 calcula MAE y veredicto, el Backoffice gobierna PAR-14 y la deriva la emite T07 (gate C1).
- **Revisás:** 04-T5 (seguridad de credenciales y cliente T07).
- **Sale de tu lista:** #3331 pasó a Damián; #3540 (pantalla de corridas) pasó a Mateo; 08-T3 pasó a Mateo.
- **Insumos para la wiki (Ana):** ejemplos reales de request y response del `run`.

## Archivos

- **Tuyos:** `reporting/services/dynamic/engine/*`, `reporting/controllers/dynamic/ReportRunController*`, fachada de corridas y estado de calibración, specs del builder y del panel.
- **No los tocás:** el catálogo y las plantillas (Joaquín), la capa de acceso (Máximo). Si necesitás una métrica nueva, **se agrega al catálogo de Joaquín**, no se hardcodea en el motor.

## Dependencias

- **Dependés de:** S2-00 · 15-T1 (Joaquín, CP3) · #311 (Máximo, CP2) · V19 (Damián, CP2) · el builder de Damián y el panel de Luciano para escribir tus specs.
- **Dependen de vos:** Damián (el builder corre contra tu `run`) y Máximo (15-T5).

## Por qué tu reparto es así

Tu núcleo es el motor, que es la ruta crítica. Las specs (15-T9 y 12-T9) se pueden entregar al final del CP5 si el motor se demora. Si C1 no llega en el CP2, tus 960 líneas condicionadas se liberan y podés acompañar a Damián o a Joaquín en lo que esté más atrasado.

## Te testean / revisan

15-T2/T4 → Máximo (15-T5), revisa Valentina (15-T7) · #3539 y 07-T2 → Máximo (06-T5, 07-T4), revisan Regina (06-T7) y Damián (07-T6).

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · invariantes verificados por 15-T5 · OpenAPI y `docs/` al día en la misma PR.
