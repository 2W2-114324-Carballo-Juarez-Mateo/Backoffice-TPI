# dev-01.md (Paz, Luciano) — Tareas Sprint 2

> **Disponibilidad:** alta · **Núcleo:** 19 u · **Con gate:** 1 u · **Stretch:** — · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md` (si algo no coincide, manda ese archivo). Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| S2-00 | **Contratos compartidos del Sprint 2** (sin lógica): `ReportScopeResolver`, `TeacherMembershipPort`, `CohortSummaryQuery`, `DataFreshnessProvider`, `DataFreshnessDto`, `RiskLevel`, `ReportMetric`, `ReportDimension` (sin `TEACHER`), DTOs de panel/run/templates/KPI, OpenAPI esqueleto y rutas stub del FE (§5) | BACK + FRONT | L | CP1 |
| H-05 | Higiene: `feature/contexto-sprint1` → PR solo de `plan-mvp-sprint1-backoffice.md` y `auditoria-contratos-skillhub.md` (sin `Contexto.md`) · `fix/development-doc-real-workflow` → rebase y PR solo de `DEVELOPMENT.md` (la regla de inglés de `AGENTS.md` ya entró por la #28) | DOC | S | CP0 |
| 06B-T1 | Orden estricto del outbox **por clave de partición** en `findReadyToPublish` (`NOT EXISTS` de una `PENDING` más vieja con la misma clave) | BACK | S | CP1 |
| 06B-T6 | Documentar el orden por clave en el contrato del consumidor | DOC | S | CP1 |
| 05-N1 | Migrar las partes 01, 02, 04 y 06 a `/api/backoffice/...` (`admin-api-url.ts`) y retirar el parche de `proxy.conf.backoffice-gateway.cjs`. **No** borrar los alias del backend | FRONT | M | CP1 |
| C6 | Seguimiento del contrato con **T10** (`sandbox.events`, vidas agotadas, agregados de retención) y fila de **T10** en la tabla de firmas de `CONTRATOS.md` | DOC | S | CP0 → CP2 |
| 04-T4 + 05-T4 | Suite WireMock del cliente T07: proveedores (200/404/409/503, key enmascarada) y activación (200/409/503) | TEST | M + M | CP3–CP4 |
| #314 + #315 | Suite de integración de HU12 en PostgreSQL real: A→A 200, A→B 403, sin pertenencia 403, ADMIN 200, `ALL` solo ADMIN, anti-comparación, `STUDENT_AT_HIGH_RISK` una sola vez | TEST | L | CP4 |
| 15-T8 | **US-15 fase 2 (Should):** builder FE (métricas, filtros, período, columnas, agrupación, "Guardar plantilla", WCAG AA) contra el contrato congelado de S2-00 | FRONT | L | CP5 |

## Con gate

| ID | Tarea | Gate |
|---|---|---|
| 08-T5 | Peer review de los consumidores T07/T05 | Contrato T07/T05 |

## Revisiones que te tocan

- **D-DEMO** (Ana): exactitud técnica del guion.
- **HU09** (Damián, stretch): export asíncrono sobre la slice 07.

## Insumos para la wiki (Ana)

Pasale los estados del outbox (`PENDING` → `PUBLISHED` / `DEAD_LETTER`) y el flujo de publicación, y revisá esa parte de su página.

## Archivos

- **Tuyos:** S2-00 (interfaces y DTOs listados en §5, hasta que cada dueño los implemente) · `OutboxMessageRepository.findReadyToPublish` · FE `08-resilience-error-api/data-access/admin-api-url.ts`, `proxy.conf*.cjs` · FE `reports/builder` (15-T8) · tests de integración de HU12.
- **No los tocás:** implementaciones de `ReportScopeResolver`/RLS (Máximo), riesgo y evento (Damián), panel (Regina). Si un test tuyo falla por código de ellos, **abrís un comentario en su PR o un issue**: no lo arreglás vos.

## Dependencias

- **Dependen de vos:** todos (S2-00 en el CP1).
- **Dependés de:** 04-T1/04-T2/05-T1 para cerrar tus suites WireMock (podés arrancar con los contratos de T07 en WireMock) · #310/#311/#312 para #314/#315.

## Te testean / revisan

S2-00 → revisan **Máximo + Mateo** · 06B-T1 → testea Máximo (06B-T2), revisa Valentina · 05-N1 → specs Mateo, revisa Joaquín · 15-T8 → specs Damián, revisa Regina.

## Si en el CP2 no llegó T10 (C6)

El factor "vidas agotadas" y las métricas de promoción/abandono quedan detrás de flag; lo registrás en `CONTRATOS.md` y pasás la tarea a "Necesita información".

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** CA de la historia, RLS verificado donde aplica, OpenAPI y docs al día.
