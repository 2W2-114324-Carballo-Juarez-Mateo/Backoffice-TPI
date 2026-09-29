# dev-08.md (Gianoli, Bruno) — Tareas Sprint 2

> **Disponibilidad:** baja · **Núcleo:** 9 u (incluye el XL de la ruta crítica) · **Con gate:** 9 u · **Stretch:** — · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| H-04 | Higiene: `feature/mvp-s6-golden-set-runs` **no se mergea** (el golden set, el MAE y el veredicto son de T07 por la Opción A; la PR #30 ya está cerrada). Crear el tag `archive/s6-golden-set-local` y borrar la rama | — | S | CP0 |
| 15-T2 + 15-T4 | **Ruta crítica de US-15 (consolidada):** motor de consultas dinámicas (filtros, período, columnas, agrupación) **y** `POST /api/backoffice/reports/run` (con `templateId` o `config`). Ejecuta dentro de `ReportScopeResolver` + RLS. Invariantes: solo métricas/dimensiones del enum, parámetros bind, sin `TEACHER`, encuestas solo agregadas con PAR-18 + curso cerrado, `DataFreshnessDto`, paginado y período máximo. **Incluye la OpenAPI del `run`** (parte de la ex 15-T6: la OpenAPI está en el código) | BACK | XL | **CP4** |
| C9 | **Nueva:** completar la fila de **T08** en la tabla de firmas de `CONTRATOS.md` (el acuerdo parcial ya está en `CONTRATOS_T08_RESPUESTA.md`) | DOC | S | CP2 |
| #3331 | Solo lectura de parámetros para PROFESSOR en el FE (el backend ya lo permite con `PARAMETER_READERS`) | FRONT | S | CP3 |
| 04-T5 | Peer review de seguridad de credenciales y del cliente T07 | REV | S | CP3 |

## Con gate (C1: T07 confirma `/api/llm/admin/*` en el CP2)

| ID | Tarea | Capa | Tamaño |
|---|---|---|---|
| #3539 | Fachada de corridas de calibración (crear, listar, detalle; `maeFinal`, `maxIndividualError` y veredicto **de T07**) | BACK | M |
| 07-T2 | Estado de calibración del modelo activo (último veredicto y deriva, leídos de T07) | BACK | M |
| #3540 | Pantalla de corridas (parte 13) | FRONT | M |
| #286 | Documentar: T07 calcula MAE y veredicto; el Backoffice gobierna PAR-14; la deriva la emite T07 | DOC | S |
| 08-T3 | IT de consumidores T07/T05: nuevo, duplicado, malformado → DLT, flag apagado (gate: contrato T07/T05) | TEST | M |

## Fuera de tu lista (vs propuesta)

- **T08-1** (replay REST de T08) → backlog: ningún reporte del S2 usa saldos y el consumidor ya existe con flag.
- **#321** (endpoint de plataforma) → Mateo, para que HU13 tenga un solo dueño.
- **15-T4** entra a tu tarea (antes era de Mateo): el `run` y el motor viven en el mismo servicio.

## Insumos para la wiki (Ana)

Pasale ejemplos reales de request/response del `run` y revisá su sección antes de que la cierre.

## Por qué quedás así

En la propuesta tenías la mayor carga relativa del equipo **y** la ruta crítica. Ahora tu núcleo es el motor. Si C1 no llega en el CP2, tus 9 unidades con gate se liberan y tomás **09-T3** (tests del export) cuando Damián lo tenga listo.

## Archivos

- **Tuyos:** `reporting/services/dynamic/engine/*`, `reporting/controllers/dynamic/ReportRunController*`, fachada de corridas y estado de calibración.
- **No los tocás:** el catálogo y las plantillas (Joaquín), la capa de acceso (Máximo). Si necesitás una métrica nueva, **se agrega al catálogo de Joaquín**, no se hardcodea en el motor.

## Dependencias

- **Dependés de:** S2-00 (DTOs y enums congelados) · 15-T1 (Joaquín, CP3) · #311 (Máximo, CP2) · V18 (Damián, CP2).
- **Dependen de vos:** Luciano (15-T8, contra el contrato congelado) y Máximo (15-T5).

## Te testean / revisan

15-T2/T4 → Máximo (15-T5), revisa Valentina (15-T7) · #3539/#3540/07-T2 → Máximo (06-T5, 07-T4), revisan Regina (06-T7) y Mateo (07-T6) · #3331 → Mateo (05-N3), revisa Joaquín (05-N4).

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** CA de US-15 fase 1, invariantes verificados por 15-T5, OpenAPI al día.
