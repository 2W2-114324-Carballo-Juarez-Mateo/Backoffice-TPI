# Tareas Sprint 2 — Backlog dividido (8 integrantes que programan) · PROPUESTA (revisada 29/09)

> **Estado:** PROPUESTA para confirmar en la planning. Modelada sobre la división del Sprint 1 (reparto parejo por capas, nadie teste ni revise su propio código, DoD con gate local).
> **Revisión previa (29/09):** se auditó lo realmente implementado en los repos oficiales (mergeado o no) y **se depuró el alcance** — gran parte del Sprint 1 ya está entregado, incluida la fachada LLM (US-04/05) y el endpoint de frescura (US-10). El Sprint 2 se enfoca en el **pipeline de reporting** (lo que falta de verdad) + el **M2M real a T07**.
> **Base:** `plan/tareas.md` · entrega real verificada: BACK `develop` `944992f` · FE `develop` `cfaac18` · UI KIT `56d322d`.

---

## 1 · Quiénes participan

| Dev | Integrante | Rol | Capacidad (h, del Excel S1) | Codifica |
|---|---|---:|---:|---|
| **1** | Paz, Luciano | MSII+PIV | 39,4 | Sí |
| **2** | Carballo Juarez, Mateo | MSII+PIV | 41,5 | Sí |
| **3** | Baigorria, Damian | PIV | 31,5 | Sí |
| **4** | Cortez, Joaquin | PIV | 27,7 | Sí |
| **6** | Maldonado, Valentina | MSII+PIV | 35,3 | Sí |
| **7** | Cerquatti, Máximo | MSII+PIV | 46,4 | Sí |
| **8** | Cerasulo, Regina | MSII+PIV | 35,3 | Sí |
| **9** | Gianoli, Bruno | PIV | 28,4 | Sí |
| **10** | Ducart, Ana Paula | MSII | 25,2 | No (solo DOC/MSII) |

> **Julieta** se cambió de grupo (S1). **Los 8 que programan** reparten BACK + FRONT + TEST + DOC (Ana) + REV.
> **Capacidad de código total: 285,5 h** (8 devs). **Carga propuesta: 194 h = 68 %.**

---

## 2 · Alcance revisado contra la entrega real

### ✅ Ya implementado en el Sprint 1 (NO entra como trabajo nuevo)

| Pieza | Evidencia en los repos oficiales |
|---|---|
| Infra (pom, compose, CI, quality) | PRs #1/#2/#17/#18/#44 |
| US-01 · Parámetros (GET/PUT, versionado, historial, Idempotency-Key, seed PAR, outbox tx) | PRs #11/#29/#39 · `ParameterController` |
| US-02 · Outbox + Kafka (envelope 6 campos, DLT, reintentos, headers de traza, resiliencia broker, idempotencia) | PRs #8/#13/#16/#22/#46 |
| US-03 · Seguridad/cuentas/auditoría (filtro Gateway, `IdentityAuditClient`, `AdministrativeAuditPublisher`, cuentas FE → `/api/users` T01, guards FE) | PRs #12/#27/#34/#37/#38 + FE slice 03/11 |
| US-08 · Ingesta acotada (consumers T03/T02, dedup `processed_event`, DLT, contadores, S8 read contracts) | PRs #9/#23/#25/#31/#45 |
| **US-10 · Frescura (endpoint)** — `GET /api/reports/health/freshness` + staleness on-read (PAR-23 / 15 min) + badge FE (slice 06) | `IngestionReportController` · FE slice 06 · PR #41 |
| **US-04/US-05 · LLM fachada** — controllers + DTOs + `LlmAdminClient` (interfaz) + `DefaultLlmAdminClient` (stub) + `LlmModelServiceImpl` (deploy/activate/retire) + FE slices 09/10 | PRs #33/#42/#36 (back) · #114 (FE) |
| EP-04 · Contratos (T11/T01/T07 cerrados, T03 acuerdo) | `plan/CONTRATOS.md` |
| Frontend backoffice (slices 01, 02, 03, 04, 06, 07, 08, 09, 10, 11, 14) | FE `develop` |

### ✅ Entra al Sprint 2 — el pipeline de reporting + M2M LLM

| Bloque | US | Horas | Por qué |
|---|---|---:|---|
| Read model de riesgo por cohorte | US-11 | 31 | Base del panel docente; NO existe en el repo |
| **Panel del profesor con indicador de alumno en riesgo + sin comparación entre docentes** | US-12 | 33 | Requisito del profe (🔴) · RLS por `course_id` + anti-comparación |
| Indicadores consolidados + bloqueo de anonimato (sin ranking docente) | US-13 | 31 | Requisito del profe (🔴) · KPIs + anti-comparación |
| Umbrales de aviso | US-14 | 22 | Cierra el pipeline de alertas (depende de US-13) |
| **Reportes docentes dinámicos (configurables)** | US-15 | 63 | Requisito del profe (🔴, SP 8) · whitelist + motor + plantillas + `reports/run` + builder FE |
| **LLM: cliente M2M real a T07** (reemplaza el stub `DefaultLlmAdminClient`) | US-04/05 | 6 | Cierra la fachada — **bloqueado por el token de T01** |
| Frescura: monitor `@Scheduled` que marca `isStale` (opcional, P2) | US-10 | 8 | El endpoint ya entrega frescura on-read; el monitor es refuerzo |
| **Total** | | **≈ 194 h** | **68 % de 285,5 h** |

### ❌ Bloqueado / fuera

| US | Motivo |
|---|---|
| **US-06 · Golden set + calibración** | ⚠️ **BLOQUEADO**: sin contrato de calibración con T07. |
| **US-07 · Aprobación por tolerancia + deriva** | Depende de US-06. |
| **US-09 · Exportación asíncrona PDF/CSV** | **Could** (la UI de export ya existe, slice 07). |

### 🧹 Cierre del Sprint 1 (no cuenta como horas de código)

- Mergear los PRs del back aún abiertos: **#47** (topics T11 v3), **#48** (placeholder ruta LLM), **#50** (contrato T01).
- **Token M2M de T01** (`audience: llm-service` + scope `llm.calibration.manage`) → desbloquea la tarea LLM M2M.
- Docs de la demo y cierre de Taiga del Sprint 1 (Ana Paula).

---

## 3 · Tareas por historia

### US-11 · Read model y cálculo de riesgo por cohorte — 31 h

> **Rama:** `feature/us-11-risk-readmodel` · 100 % backend (resultado se expone en US-12/US-13).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Migración + esquema `reporting.cohort_summary(course_id, student_id, activity_days, approval_rate, last_activity_at, risk_level)` + índices | BACKEND | Damian | 8 |
| T2 | Algoritmo de clasificación: ROJO = ≥14 días sin actividad O <40 % aprobación · AMARILLO = ≥7 días · VERDE = reciente + ≥80 % | BACKEND | Bruno | 8 |
| T3 | Job programado de recálculo periódico | BACKEND | Joaquin | 4 |
| T4 | Tests de partición de equivalencia + valores límite (13 vs 14 días, 39 % vs 40 %) | TEST | Máximo | 6 |
| T5 | Documentar reglas de cálculo + esquema del read model | DOCUMENTACION | Ana Paula | 3 |
| T6 | Peer review de modelado analítico + índices | REVISION | Valentina | 2 |

### US-12 · Panel del docente con RLS y alerta de riesgo — 33 h · 🔴 requisito del profe

> **Rama:** `feature/us-12-teacher-panel` · **Depende de:** US-11 + pertenencia docente de T02.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | `GET /api/reports/courses/{courseId}/teacher` + validación de pertenencia vía T02 (403 si no pertenece) | BACKEND | Valentina | 6 |
| T2 | RLS por `course_id` + regla anti-comparación (`app.current_course` desde contexto validado) | BACKEND | Regina | 6 |
| T3 | Evento `StudentAtHighRisk` → `notifications.events` (T11) ante transición a riesgo alto | BACKEND | Mateo | 2 |
| T4 | FE: vista de panel docente con semáforo 🔴🟡🟢 + filtros + tarjetas de comisión | FRONTEND | Damian | 6 |
| T5 | Tests RLS: PROFESOR A → cohorte A 200 · cohorte B 403 · intento ALL 403 · ADMIN 200 | TEST | Luciano | 6 |
| T6 | Tests: anti-comparación (sin datos de otros docentes) + emisión de la alerta | TEST | Máximo | 2 |
| T7 | Documentar endpoints del panel, RLS y contrato de alerta | DOCUMENTACION | Ana Paula | 3 |
| T8 | Peer review de seguridad RLS (punto CRÍTICO) | REVISION | Bruno | 2 |

### US-13 · Indicadores consolidados con bloqueo de anonimato — 31 h · 🔴 requisito del profe

> **Rama:** `feature/us-13-kpis` · **Depende de:** US-11.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Servicio de agregación (CSAT 5 estrellas, engagement, tasas de aprobación/abandono) sobre los read models | BACKEND | Joaquin | 6 |
| T2 | Protección de anonimato por umbral mínimo (< 5 respuestas → "muestra insuficiente") | BACKEND | Valentina | 4 |
| T3 | `GET /api/reports/platform` exclusivo ADMIN + anti-comparación (sin ranking docente) | BACKEND | Bruno | 4 |
| T4 | FE: dashboard de KPIs con tarjetas CSAT + banner "muestra insuficiente" | FRONTEND | Mateo | 6 |
| T5 | Tests: anonimato (3/4/5 respuestas) + no-ADMIN → 403 | TEST | Regina | 6 |
| T6 | Documentar políticas de privacidad + fórmulas de agregación | DOCUMENTACION | Ana Paula | 3 |
| T7 | Peer review de privacidad (sin comparación docente) | REVISION | Luciano | 2 |

### US-14 · Umbrales de aviso y acceso al tablero — 22 h

> **Rama:** `feature/us-14-thresholds` · **Depende de:** US-13 (indicadores calculados).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Modelo `alert_thresholds(indicator, min_value, max_value, enabled)` + CRUD exclusivo ADMIN | BACKEND | Regina | 4 |
| T2 | Evaluador periódico de métricas contra umbrales + `ThresholdBreached` (baja → alerta) | BACKEND | Mateo | 6 |
| T3 | FE: panel de configuración de umbrales + lista de alertas activas | FRONTEND | Luciano | 4 |
| T4 | Tests: evaluación en el límite y 1 punto abajo + no-ADMIN 403 | TEST | Máximo | 4 |
| T5 | Documentar catálogo de umbrales + evento `ThresholdBreached` | DOCUMENTACION | Ana Paula | 2 |
| T6 | Peer review de consistencia del backlog de reporting | REVISION | Joaquin | 2 |

### US-15 · Reportes docentes dinámicos (configurables) — 63 h · 🔴 P0 (requisito del profe)

> **Rama:** `feature/us-15-dynamic-reports` · **Depende de:** US-11 (read models) + pertenencia T02 + anti-comparación.
> El PROFESOR arma reportes eligiendo **métricas, filtros, período, columnas y agrupación** y **guarda** la configuración (plantilla).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Catálogo de métricas permitidas (whitelist): CSAT, engagement, aprobación/abandono, promoción, riesgo, distribución XP, actividad semanal — **sin expresiones arbitrarias** | BACKEND | Damian | 8 |
| T2 | Motor de query dinámico (filtros/período/columnas/agrupación) sobre read models con **RLS por `course_id`** | BACKEND | Máximo | 12 |
| T3 | CRUD de plantillas y favoritas (`report_template(owner_id, course_id, config jsonb, is_favorite)`) | BACKEND | Valentina | 8 |
| T4 | Ejecución `POST /api/reports/run` (con `templateId` o `config`) + invariantes: matrícula T02, anti-comparación (RF-RPT-07), anonimato (encuestas solo agregados), frescura ≤ 15 min | BACKEND | Mateo | 8 |
| T5 | FE: vista report builder (panel métricas/filtros/período/columnas + "Guardar plantilla") | FRONTEND | Luciano | 12 |
| T6 | Tests del motor dinámico: RLS (PROFESOR A → cohorte A 200 · cohorte B 403), anti-comparación, anonimato | TEST | Regina | 8 |
| T7 | OpenAPI de templates/run + catálogo de métricas | DOCUMENTACION | Ana Paula | 4 |
| T8 | Peer review de seguridad del motor (RLS/whitelist) | REVISION | Joaquin | 3 |

### US-04/05 · Cierre de la fachada LLM — cliente M2M real a T07 — 6 h

> **Rama:** `feature/us-04-llm-m2m` · **Depende de:** token M2M de T01 (`audience: llm-service` + scope `llm.calibration.manage`).
> El resto de US-04/05 (controllers, DTOs, `LlmModelServiceImpl`, FE slices 09/10) **ya está mergeado** (PRs #33/#42/#36 + #114).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Implementar el cliente HTTP real `LlmAdminClient` a `llm-service` (`/admin/evaluator-models`, `/active`, `activate`, `select-for-calibration`, `usage`, `DELETE {deploymentId}`) reemplazando `DefaultLlmAdminClient` stub; 503 controlado si T07 no responde | BACKEND | Máximo | 6 |

### US-10 · Frescura — monitor `@Scheduled` (opcional, P2) — 8 h

> **Rama:** `feature/us-10-freshness-monitor` · El endpoint `/health/freshness` y el badge FE **ya están en develop**; esto es refuerzo.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Monitor `@Scheduled` que marca `isStale` en `IngestionCounter` cuando `now − last_event_at > PAR-23` (15 min) y lo normaliza al llegar datos (si el equipo lo considera necesario) | BACKEND | Bruno | 8 |

---

## 4 · Resumen de carga por integrante

| Dev | Integrante | BACK | FRONT | TEST | REV | **Total** | Capacidad | % uso |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Luciano | — | 16 (US-14, US-15) | 6 (US-12) | 2 (US-13) | **24** | 39,4 | 61 % |
| 2 | Mateo | 14 (US-12, US-14, US-15) | 6 (US-13) | — | — | **22** | 41,5 | 53 % |
| 3 | Damian | 16 (US-11, US-15) | 6 (US-12) | — | — | **22** | 31,5 | 70 % |
| 4 | Joaquin | 10 (US-11, US-13) | — | — | 5 (US-14, US-15) | **15** | 27,7 | 54 % |
| 6 | Valentina | 18 (US-12, US-13, US-15) | — | — | 2 (US-11) | **20** | 35,3 | 57 % |
| 7 | Máximo | 18 (US-15 motor, US-04 M2M) | — | 12 (US-11, US-12, US-14) | — | **30** | 46,4 | 65 % |
| 8 | Regina | 10 (US-12, US-14) | — | 14 (US-13, US-15) | — | **24** | 35,3 | 68 % |
| 9 | Bruno | 12 (US-11, US-13) + 8 (US-10 P2) | — | — | 2 (US-12) | **22** | 28,4 | 77 % |
| 10 | Ana Paula | — | — | — | — | **15 (DOC)** | 25,2 | MSII |

> **Total código: 179 h + 15 h DOC = 194 h · 68 % de 285,5 h.** Nadie supera su capacidad (pico: Bruno 77 %).
> **Frontend acotado** (US-12, US-13, US-14, US-15): el resto de la UI ya se entregó en S1.
> **Joaquin (54 %)** tiene margen: recibe el overflow de tests/reviews si un compañero se traba.
> **Luciano** suma coordinación/ops (no cuenta como horas): compose, M2M T07, gateway/proxy y reviews de integración.

---

## 5 · Secuencia de merges sugerida

| PR | Rama | Contenido | Merge |
|---|---:|---|---|
| #0 (cierre S1) | `#47`/`#48`/`#50` | topics T11 v3, placeholder ruta, contrato T01 | Día 1 |
| 1 | `feature/us-11-risk-readmodel` | migración + algoritmo + job | Día 4–5 |
| 2 | `feature/us-13-kpis` | indicadores + anonimato | Día 6–7 |
| 3 | `feature/us-12-teacher-panel` | panel + RLS (CRÍTICO) | Día 8 |
| 4 | `feature/us-14-thresholds` | umbrales + alertas | Día 9 |
| 5 | `feature/us-15-dynamic-reports` | whitelist + motor + plantillas + `reports/run` + builder | Día 10–11 |
| 6 | `feature/us-04-llm-m2m` | cliente real T07 (si llega el token) | Día 10 |
| 7 | `feature/us-10-freshness-monitor` | monitor `@Scheduled` (P2, opcional) | Día 11 |

> Los FE van en sus propias ramas (`feature/<us>-ui`) y se integran antes de cada merge de backend.
> **Regla:** nadie abre un PR sin `mvn clean verify` en verde (gate local, como S1); el revisor lo re-corre.

---

## 6 · Decisiones a confirmar en la planning

| # | Decisión | Recomiendo |
|---|---|---|
| D1 | ¿El monitor `@Scheduled` de frescura entra (P2)? | Opcional: el endpoint ya entrega frescura on-read ≤ 15 min |
| D2 | ¿M2M real a T07 depende del token de T01? | Sí — Máximo lo desbloquea; el stub queda hasta entonces |
| D3 | ¿US-06/US-07 quedan bloqueadas? | Sí, hasta cerrar contrato de calibración con T07 |
| D4 | ¿US-09 (exportación) fuera? | Sí, Could |
| D5 | ¿Ana carga Taiga y documenta todo? | Sí (única con DOC) |

---

> **Cobertura de los requisitos del profe:** ✅ **Panel del profesor con indicador de alumno en riesgo** (US-12) · ✅ **Frescura máxima de 15 min** (US-10, endpoint ya en develop + monitor P2) · ✅ **Sin comparación entre docentes** (US-12 RLS anti-comparación + US-13 sin ranking) · ✅ **Reportes docentes** (US-15).