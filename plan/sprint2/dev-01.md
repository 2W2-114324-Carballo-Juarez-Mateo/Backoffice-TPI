# dev-01.md (Paz, Luciano) — Tareas Sprint 2

> **Núcleo:** 2.925 líneas · **Condicionado y extra:** 1.050 · **Total techo:** 3.975 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

> **Etiquetas de tipo de trabajo** (para cargar en Taiga, una o más por tarea): Backend · Frontend · Testing · Base de Datos · DevOps · Documentación · Análisis · Diseño / UX-UI · Integración · Configuración · Seguridad · Investigación · Gestión · Otro. Criterio completo e índice maestro en `etiquetas-tareas.md`.

## Núcleo

| ID | Tarea | Capa | Líneas | CP | Etiquetas |
|---|---|---|---:|---|---|
| S2-00 | **Contratos compartidos del Sprint 2** (sin lógica): `ReportScopeResolver`, `TeacherMembershipPort`, `CohortSummaryQuery`, `DataFreshnessProvider`, `DataFreshnessDto`, `RiskLevel`, `ReportMetric`, `ReportDimension` (sin `TEACHER`), DTOs de panel/run/plantillas/KPI, OpenAPI esqueleto y rutas stub del FE | BACK + FRONT | 550 | **CP1** | Backend, Frontend, Integración |
| 06B-T1 | Orden estricto del outbox por **clave de partición** en `findReadyToPublish` (`NOT EXISTS` de una `PENDING` más vieja con la misma clave; una `DEAD_LETTER` no bloquea) | BACK | 65 | CP1 | Backend, Base de Datos |
| 05-N1 | Migrar las partes 01, 02, 04 y 06 a `/api/backoffice/...` (`admin-api-url.ts`) y retirar el parche de `proxy.conf.backoffice-gateway.cjs`. No borrar los alias del backend | FRONT | 350 | CP1 | Frontend, Integración |
| 04-T4 + 05-T4 | Suites WireMock del cliente T07: proveedores (200/404/409/503, key enmascarada) y activación (200/409/503) | TEST | 560 | CP3–CP4 | Testing, Integración |
| #314 + #315 | Suite de integración de HU12 en PostgreSQL real: A→A 200, A→B 403, sin pertenencia 403, ADMIN 200, `ALL` solo ADMIN, anti-comparación, `STUDENT_AT_HIGH_RISK` una sola vez | TEST | 600 | CP4 | Testing, Seguridad, Base de Datos |
| #313 | **Panel docente con semáforo** (FE): color **y** texto, teclado, WCAG AA, badge de frescura; contra `TeacherPanelResponseDto` (pasó de Damián a vos) | FRONT | 800 | CP4 | Frontend, Diseño / UX-UI |
| | **Subtotal núcleo** | | **2.925** | | |

## Condicionado y extra

| ID | Tarea | Capa | Líneas | Condición | Etiquetas |
|---|---|---|---:|---|---|
| #1657 | Estado de 2FA y sesión (FE) | FRONT | 300 | Gate: T01 | Frontend, Seguridad |
| 14-T3 | Panel de umbrales + lista de alertas activas (FE de HU14) | FRONT | 750 | Extra (solo con el núcleo mergeado) | Frontend, Diseño / UX-UI |
| | **Subtotal** | | **1.050** | | |

## Sin líneas de código (documentación y revisión)

- **H-05:** PR de `plan-mvp-sprint1-backoffice.md` y `auditoria-contratos-skillhub.md` (sin `Contexto.md`); rebase y PR de `DEVELOPMENT.md`. *Etiquetas: Documentación, Gestión.*
- **C6:** seguimiento con T10 (vidas agotadas, `sandbox.events`) y fila de T10 en la tabla de firmas. Avisarle que **PAR-12 se lee del registro** apenas se mergee la PR #56. *Etiquetas: Integración, Gestión.*
- **06B-T6:** documentar el orden por clave en el contrato del consumidor. *Etiquetas: Documentación.*
- **Revisás:** 08-T5 (consumidores de Valentina) y HU09 (Damián y Valentina). *Etiquetas: Testing.*
- **Insumos para la wiki (Ana):** estados del outbox (`PENDING` → `PUBLISHED` / `DEAD_LETTER`) y ejemplos del panel docente.

## Archivos

- **Tuyos:** interfaces y DTOs de S2-00 (hasta que cada dueño los implemente), `OutboxMessageRepository.findReadyToPublish`, FE `admin-api-url.ts`, `proxy.conf*.cjs`, FE panel docente, tests de integración de HU12.
- **No los tocás:** implementaciones de `ReportScopeResolver` y RLS (Máximo), riesgo y evento (Damián), endpoint del panel (Regina). Si tus tests fallan por código de ellos, comentario en su PR.

## Dependencias

- **Dependen de vos:** todos (S2-00 en el CP1).
- **Dependés de:** 04-T1 y 05-T1 para cerrar tus suites (arrancás con WireMock) · #310, #311, #312 para #314/#315 · `TeacherPanelResponseDto` (S2-00) para #313.

## Te testean / revisan

S2-00 → revisan Máximo y Mateo · 06B-T1 → testea Regina, revisa Valentina · 05-N1 y #1657 → specs Mateo, revisa Joaquín · #313 → specs Bruno (12-T9), revisa Mateo · 14-T3 → tests Máximo, revisa Joaquín.

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · OpenAPI y `docs/` al día en la misma PR.
