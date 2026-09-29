# dev-02.md (Carballo Juarez, Mateo) — Tareas Sprint 2

> **Disponibilidad:** alta · **Núcleo:** 16 u · **Con gate:** 1 u · **Stretch:** — · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| H-01 / 01-IT | Rescatar **solo** `GlobalParameterServiceIntegrationTest` de `feature/us-01-testcontainers` sobre `develop` (rama nueva `feature/tema-12-us01-parameter-it`). **Descartar** el `jsonKafkaTemplate` de `KafkaProducerConfig`: la auditoría ya va por outbox. Adaptar el test a la API actual del registro (PAR-01..24, historial) | TEST | M | CP1 |
| H-02 | Higiene: cerrar sin mergear `feature/contratos-alineados-drive` y `feature/us-02-envelope` (duplica `EventEnvelope` y el armado del envelope que ya hace `DomainEventOutboxImpl`) · FE: borrar `fix/admin-export-service-spec` y los 3 commits sueltos de `feature/tema-12-backoffice` (`develop` ya tiene tu versión más estricta: `3899e9d` + `495e29b`) | — | S | CP0 |
| 05-T1 | Cliente real de modelos evaluadores (listar, activo, desplegar, activar, borrar) sobre la infraestructura de 04-T1 | BACK | M | CP3 |
| #276 | Modal de conmutación con advertencia (actual → nuevo, confirmación explícita, textos de UI en español) | FRONT | S | CP3 |
| 05-T3 | Pantalla 10 conectada: quitar `MOCK_MODELS` de `llm-models.service.ts` | FRONT | M | CP3 |
| HU13 (#319/#320/#321) | **Consolidada:** `V22__reporting_survey_summary.sql` (conteos por estrella, abstenciones, dimensión, `course_closed`, **sin autor ni timestamp preciso**, con política RLS) · KPI-01/02 = % 4–5 y % 1–2 sobre respuestas emitidas · abstenciones aparte (RF-ENC-10) · PAR-18 **y** curso cerrado para el PROFESOR (RF-ENC-13) · `GET /reports/courses/{courseId}/kpis` y `GET /reports/platform` (solo ADMIN, desglose por curso **sin ranking**) · `DataFreshnessDto` | BACK | L | CP4 |
| 05-N3 | Specs de guards y de la vista de solo lectura (ADMIN, GESTOR, PROFESSOR) | TEST | M | CP4 |
| C2 | **Reducida:** confirmar con T11 la materialización de `notifications.events`, el payload de `STUDENT_AT_HIGH_RISK` y `EXPORT_READY`, y si registran `THRESHOLD_BREACHED` (los topics ya están ratificados desde la PR #47) | DOC | S | CP0 → CP2 |
| #307 | Documentar reglas de riesgo y esquema del read model | DOC | S | CP3 |
| #317 | Peer review de seguridad RLS (crítico) de #310/#311/#312/#313 | REV | S | CP3–CP4 |

## Con gate

| ID | Tarea | Gate |
|---|---|---|
| 07-T6 | Peer review de HU07 (y de 07-T1/P-12 de Damián, que no tienen gate) | C1 |

## Revisiones que te tocan

- **S2-00** (Luciano) junto con Máximo.
- **H-06** release `v1.0.0` (PR #54, Damián).

## Insumos para la wiki (Ana)

Pasale ejemplos reales de request/response de modelos LLM y de los KPIs, y revisá su sección antes de que la cierre.

## Archivos

- **Tuyos:** `services/llm/model/impl/*` (cliente real), FE `10-llm-models/*`, `reporting/**/kpi/*`, `V22`.
- **No los tocás:** `RestClient` de T07 (Máximo, 04-T1), `ReportScopeResolver`/RLS (Máximo), el badge de frescura (Valentina). Los usás por su interfaz.

## Dependencias

- **Dependés de:** 04-T1 (Máximo, CP2) para 05-T1 · S2-00 (`CsatKpiDto`, `ReportScopeResolver`) · C3 de Damián (sin datos de T02 el KPI devuelve "sin datos", no se bloquea el desarrollo).
- **Dependen de vos:** Valentina (#322 consume tus KPIs; con el DTO congelado puede arrancar con mocks).

## Te testean / revisan

05-T1/#276/05-T3 → Luciano (05-T4) y Valentina (05-T7), revisa Joaquín (05-T6) · HU13 → Joaquín (#323), revisa Damián (#325) · 01-IT → revisa Regina.

## Si en el CP2 no llegó T02 (C3)

HU13 se termina contra el DTO y los tests con datos sintéticos; el endpoint responde "sin datos" y la historia queda con la fuente marcada como pendiente.

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** CA de la historia, RLS verificado, OpenAPI y docs al día.
