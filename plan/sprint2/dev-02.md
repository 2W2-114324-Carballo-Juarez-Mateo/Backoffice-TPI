# dev-02.md (Carballo Juarez, Mateo) — Tareas Sprint 2

> **Núcleo:** 2.990 líneas · **Condicionado y extra:** 1.050 · **Total techo:** 4.040 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

> **Etiquetas de tipo de trabajo** (para cargar en Taiga, una o más por tarea): Backend · Frontend · Testing · Base de Datos · DevOps · Documentación · Análisis · Diseño / UX-UI · Integración · Configuración · Seguridad · Investigación · Gestión · Otro. Criterio completo e índice maestro en `etiquetas-tareas.md`.

## PRs de Mateo — estado (1/10)

| PR | Qué | Estado | Falta para mergear |
|---|---|---|---|
| **PR #56** | PAR-14 / PAR-12 y alineación con Skill Hub (rama `feature/docs-params-skillhub`) | **Abierta y sincronizada** con `develop` (`state=clean`, ahead=6, base `10098ad`) | Review de Damián; confirmar con T07 el rename `promedio → average` |
| **#276** | Modal de conmutación (rama `feature/tema-12-model-switch-modal`) | **Implementada** · `npm run verify` en verde (226 archivos / 2204 tests) | Push + abrir PR; testean Luciano/Valentina, revisa Joaquín |
| **#4522 (01-IT)** | IT de US-01 (rama `feature/tema-12-us-01-it`) | **Implementada** · commit `73a5696` | Push + abrir PR; requiere **V24 (PR #56) mergeado primero**; revisa Regina |
| **#307** | Doc reglas de riesgo + read model (rama `feature/tema-12-riesgo-doc`) | **Implementada** (docs en back + workspace) · commit `a5f4b66` | Push + abrir PR |

> **DoD PR:** `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · OpenAPI y `docs/` al día en la misma PR.

## Núcleo

| ID | Tarea | Capa | Líneas | CP | Etiquetas |
|---|---|---|---:|---|---|
| **PR #56** | **PAR-14, PAR-12 y alineación con Skill Hub: ya está hecha y abierta, queda asignada a vos** (ex 07-T1 y P-12 de Damián). Antes de mergear: confirmar con T07 el renombre `promedio → average` (migración sobre un valor ya cargado), sincronizar con `develop` y **usar V24** | BACK | 260 | **CP0** | Backend, Base de Datos, Configuración |
| 01-IT | Test de integración de US-01 rescatado de `feature/us-01-testcontainers` (solo el test, sin `jsonKafkaTemplate`), adaptado al registro actual | TEST | 180 | CP1 | Testing, Base de Datos |
| 05-T1 | Cliente real de modelos evaluadores (listar, activo, desplegar, activar, borrar) sobre la infraestructura de 04-T1 | BACK | 600 | CP3 | Backend, Integración |
| #276 | Modal de conmutación con advertencia (actual → nuevo, confirmación explícita, UI en español) | FRONT | 300 | CP3 | Frontend, Diseño / UX-UI |
| 05-T3 | Pantalla 10 conectada: quitar `MOCK_MODELS` de `llm-models.service.ts` | FRONT | 450 | CP3 | Frontend, Integración |
| 05-N3 | Specs de guards y de la vista de solo lectura (ADMIN, GESTOR, PROFESSOR) | TEST | 350 | CP4 | Testing, Frontend, Seguridad |
| #322 | **Dashboard de KPIs** con "muestra insuficiente" y "disponible al cierre del curso", contra `CsatKpiDto` (pasó de Valentina a vos) | FRONT | 850 | CP4 | Frontend, Diseño / UX-UI |
| | **Subtotal núcleo** | | **2.990** | | |

## Condicionado y extra

| ID | Tarea | Capa | Líneas | Condición | Etiquetas |
|---|---|---|---:|---|---|
| #3540 | Pantalla de corridas de calibración (parte 13) | FRONT | 750 | Gate: C1 (T07) | Frontend, Integración |
| 08-T3 | IT de consumidores T07/T05: nuevo, duplicado, malformado → DLT, flag apagado | TEST | 300 | Gate: contrato T07/T05 | Testing, Integración |
| | **Subtotal** | | **1.050** | | |

## Sin líneas de código (documentación y revisión)

- **H-02:** cerrar sin mergear `feature/contratos-alineados-drive` y `feature/us-02-envelope`; en el FE, cerrar `fix/admin-export-service-spec`. *Etiquetas: Gestión, DevOps.*
- **C2:** confirmar con T11 la materialización de `notifications.events` y el payload de `STUDENT_AT_HIGH_RISK`/`EXPORT_READY`; fila de T11 en la tabla de firmas. *Etiquetas: Integración, Gestión.*
- **#307:** documentar reglas de riesgo (incluidas R-1 y R-2) y esquema del read model. *Etiquetas: Documentación, Análisis.*
- **Revisás:** #317 (seguridad RLS, crítico) · back-merge de la release v1.0.0 (PR #58, que trae **V18**: mergearla primero) · 01-IT queda revisado por Regina. *Etiquetas: Testing.*
- **Insumos para la wiki (Ana):** ejemplos de modelos LLM y cómo se alinearon los parámetros con Skill Hub.

## Archivos

- **Tuyos:** `ParameterValueRules` y su test, `V24`, `services/llm/model/impl/*`, FE `10-llm-models/*`, FE dashboard de KPIs, FE pantalla de corridas.
- **No los tocás:** el `RestClient` de T07 (Máximo, 04-T1), el endpoint de KPIs (Joaquín): lo usás por DTO.

## Dependencias

- **Dependés de:** 04-T1 (Máximo, CP2) para 05-T1 · `CsatKpiDto` (S2-00) y el endpoint de HU13 (Joaquín, CP4) para #322: arrancás con mocks.
- **Dependen de vos:** la PR #56 fija los PAR que leen T03, T05, T08 y T10 (`average` y PAR-12).

## Te testean / revisan

PR #56 → revisa Damián · 01-IT → revisa Regina · 05-T1, #276 y 05-T3 → Luciano (05-T4) y Valentina (05-T7), revisa Joaquín · #322 → revisa Damián (#325) · #3540 → Máximo (06-T5/07-T4), revisa Regina (06-T7).

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · OpenAPI y `docs/` al día en la misma PR.
