# dev-04.md (Cortez, Joaquín) — Tareas Sprint 2

> **Disponibilidad:** baja · **Núcleo:** 12 u · **Con gate:** 5 u · **Stretch:** — · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| V21 | **Arrastre S1 (tu registro de contratos, PR #45):** `V21__reporting_source_contract_v3.sql` con `UPDATE` de los topics del seed de V14 (`courses.lifecycle` → `courses.events`, `challenges.results` → `challenges.events`, `economy.transactions` → `accounting.events`) y ajuste de `ReadContractControllerTest` y `SourceContractRepositoryTest` | BACK | S | **CP1** |
| 15-T1 | Catálogo de métricas de US-15 (lista blanca por enum) con fuente y disponibilidad según el contrato; `GET /reports/metrics`. **Incluye su OpenAPI** (parte de la ex 15-T6: la OpenAPI está en el código) | BACK | M | CP3 |
| 15-T3 | CRUD de plantillas y favoritas, solo del dueño: `V20__reporting_report_template.sql` (`owner_id, course_id, config jsonb, is_favorite`). El `config` se valida contra la lista blanca de 15-T1 al guardar. **Incluye su OpenAPI** | BACK | M | CP3 |
| 12-T9 | Specs del panel docente (#313) | TEST | S | CP4 |
| #323 | **Nueva para vos:** tests de anonimato de HU13: 4, 5 y 6 respuestas con PAR-18 = 5; curso abierto → sin puntajes; no-ADMIN en `/platform` → 403 | TEST | M | CP4 |
| #324 | Políticas de privacidad y fórmulas de los KPIs | DOC | S | CP4 |
| 05-T6 | Peer review de la fachada de modelos (Mateo) | REV | S | CP3 |
| 05-N4 | Peer review de guards, migración de rutas, badge y 2FA | REV | S | CP1–CP4 |
| 14-T6 | Peer review de HU14 (si se hace el stretch) | REV | S | CP5 |

## Con gate (C1: T07 confirma `/api/llm/admin/*` en el CP2)

| ID | Tarea | Capa | Tamaño |
|---|---|---|---|
| #3537 | Fachada del perfil de calibración institucional (golden set y rúbrica) sobre T07 | BACK | M |
| #3538 | Pantalla del perfil de calibración (parte 12) | FRONT | M |
| 06-T8 | OpenAPI de la fachada de calibración | DOC | S |

## Insumos para la wiki (Ana)

Pasale la tabla de contratos (V14/V21) para el DER, ejemplos reales de métricas y plantillas, y revisá su sección antes de que la cierre.

## Archivos

- **Tuyos:** `V20`, `V21`, `reporting/**/dynamic/ReportMetric*` (catálogo), `reporting/**/templates/*`, fachada de calibración (perfil).
- **No los tocás:** el motor y el `run` (Bruno). Tu contrato con él es el enum de métricas/dimensiones de S2-00: si necesitás una métrica nueva, **agregala al catálogo y avisale**, no la metas en el motor.

## Dependencias

- **Dependen de vos:** Bruno (el motor usa tu catálogo) y Luciano (el builder lista tus métricas y plantillas).
- **Dependés de:** S2-00 (enums y DTOs congelados).

## Te testean / revisan

15-T1/15-T3 → Máximo (15-T5), revisa Valentina (15-T7) · V21 → revisa Valentina · #3537/#3538 → Máximo (06-T5), revisa Regina (06-T7).

## Si en el CP2 no llegó C1

#3537/#3538/06-T8 quedan con stub + flag y pasan a "Necesita información". Si te sobra margen, tomás **09-T3** (tests del export) cuando Damián lo tenga listo.

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** CA de US-15 fase 1, lista blanca verificada, OpenAPI al día.
