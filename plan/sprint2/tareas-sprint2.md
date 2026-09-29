# Tareas Sprint 2 — Backlog dividido (8 integrantes que programan) · PROPUESTA

> **Estado:** PROPUESTA para confirmar en la planning. Modelada sobre la división del Sprint 1 (reparto parejo por capas, nadie teste ni revise su propio código, DoD con gate local).
> **Base:** `plan/tareas.md` (backlog general, 15 US) · entrega real del Sprint 1 verificada en los repos oficiales (BACK `944992f` · FE `cfaac18` · UI KIT `56d322d`).
> **Repo de entrega:** `2026-P4-BE/tpi-backoffice` (mono-módulo) + `2026-P4-FE/2026-PIV-TPI-FE` + UI KIT `@2026-p4-fe/ui`.
> **Flujo de ramas:** `feature/<algo>` / `fix/<algo>` desde `develop` → PR → review → `develop`.

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
> **Capacidad de código total: 285,5 h** (8 devs). Objetivo sano: **65–70 % ≈ 185–200 h**.

---

## 2 · Alcance: qué entra y qué no (y por qué)

### ✅ Entra — circuito de reporting + fachada LLM real

| Bloque | US | Horas | Motivo |
|---|---|---:|---|
| Frescura (completar) | US-10 | 20 | El monitor y `health/freshness` quedaron parciales en S1 (tests BDD mergeados, endpoint/badge pendientes) |
| Read model de riesgo | US-11 | 31 | Base de todo el panel docente (Sprint 1 lo difirió por falta de datos reales) |
| Panel docente con RLS | US-12 | 33 | Demostrable con read model + pertenencia T02 |
| Indicadores consolidados + anonimato | US-13 | 31 | KPI de plataforma + salvaguarda de privacidad |
| Modelos IA — fachada real a T07 | US-04 | 25 | S1 entregó la UI (slices 09/10) y el stub; falta el cliente real (`llm-service` v2.0.0, Skill Hub, contrato cerrado) |
| Conmutación de modelos | US-05 | 25 | Da valor a US-04 (activar → ACTIVE/RESERVE + evento `MODEL_PROVIDER_CHANGED`) |
| Umbrales de aviso | US-14 | 22 | Depende de los indicadores de US-13; cierra el pipeline de alertas |
| **Total** | | **≈ 187 h** | **65 % de 285,5 h** |

### 🔶 Sale / se difiere

| US | Motivo |
|---|---|
| **US-06 · Golden set + calibración** | ⚠️ **BLOQUEADO**: el contrato de calibración con T07 no está cerrado (quién ejecuta el golden set). **No cargar hasta coordinar con T07/cátedra.** |
| **US-07 · Aprobación por tolerancia + deriva** | Depende de US-06 (calibración funcional). Entra cuando T07 habilite la corrida. |
| **US-09 · Exportación asíncrona PDF/CSV** | **Could.** La UI de export (slice 07) ya existe; el backend asíncrono queda para cuando haya margen. |
| **US-15 · Reportes docentes dinámicos** | Requisito del profe (SP 8 · back ≈ 48 h). No entra por capacidad; candidato fuerte para Sprint 3 o bloque propio. |

### 🧹 Cierre del Sprint 1 (no cuenta como código)

- Mergear los PRs del back aún abiertos: **#47** (topics T11 v3), **#48** (placeholder ruta LLM), **#50** (contrato T01 registrado).
- **M2M real a T07:** reemplazar el `DefaultLlmAdminClient` stub por el cliente real cuando T01 entregue el token (`audience: llm-service` + scope `llm.calibration.manage`). Coordinar con Máximo.
- Docs de la demo y cierre de Taiga del Sprint 1 (Ana Paula).

---

## 3 · Tareas por historia

### US-04 · Modelos IA — fachada real a T07 (registro proveedores/modelos) — 25 h

> **Rama:** `feature/us-04-llm-facade` · **Depende de:** contrato T07 (CERRADO en Skill Hub: `llm-service-http-contract` v6, scope `llm.calibration.manage`, path var `{deploymentId}`) + token M2M de T01.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Cliente real `LlmAdminClient` a T07 (reemplaza `DefaultLlmAdminClient` stub): GET/POST `/admin/evaluator-models`, activate/select-for-calibration/usage, DELETE `{deploymentId}` | BACKEND | Máximo | 6 |
| T2 | Catálogo de proveedores/modelos con estados y `credentialId` reales desde T07 + enmascaramiento | BACKEND | Regina | 4 |
| T3 | Registro de modelo → `PENDING_REVIEW` + bloqueo de activación (409) | BACKEND | Bruno | 4 |
| T4 | Tests: seguridad de scope `llm.calibration.manage` (sin scope → 403) + auth ADMIN/PROFESSOR | TEST | Joaquin | 4 |
| T5 | Tests: bloqueo de activación en `PENDING_REVIEW` | TEST | Mateo | 2 |
| T6 | Documentar endpoints en OpenAPI + máquina de estados | DOCUMENTACION | Ana Paula | 3 |
| T7 | Peer review de seguridad de credenciales/scopes | REVISION | Luciano | 2 |

### US-05 · Sustitución y conmutación de modelos — 25 h

> **Rama:** `feature/us-05-model-switch` · **Depende de:** US-04.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | `POST …/activate` validando `APPROVED`/`RESERVE` (409 en otro estado) + **un solo `ACTIVE` por función** | BACKEND | Damian | 6 |
| T2 | Transición `ACTIVE` ↔ `RESERVE` + persistir `ModelProviderChanged` en el outbox (misma transacción) | BACKEND | Joaquin | 6 |
| T3 | Publicar `ModelProviderChanged` (payload: `previousModelId`, `newModelId`, `function`, `activatedBy`, `activatedAt`) | BACKEND | Valentina | 2 |
| T4 | FE: modal de conmutación con advertencia de impacto (slice 10) | FRONTEND | Regina | 3 |
| T5 | Tests: transiciones válidas/inválidas, 409, evento con modelo anterior y nuevo | TEST | Máximo | 4 |
| T6 | Documentar contrato `ModelProviderChanged` + OpenAPI | DOCUMENTACION | Ana Paula | 2 |
| T7 | Peer review de unicidad del activo + atomicidad | REVISION | Bruno | 2 |

### US-10 · Control de frescura (completar) — 20 h

> **Rama:** `feature/us-10-freshness` · S1 dejó los escenarios BDD (`#41`) y parte del backend.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Monitor programado de frescura por tema (@Scheduled, SLA 15 min) + marcado `isStale` | BACKEND | Bruno | 4 |
| T2 | `GET /api/reports/health/freshness` + auto-retirar la marca al normalizar | BACKEND | Valentina | 4 |
| T3 | FE: badge de frescura en la cabecera de reportes ("sincronizado" vs "hace X min") | FRONTEND | Luciano | 4 |
| T4 | Tests: BDD frescura/normalización (16 min → stale, dato llega → marca retirada) | TEST | Joaquin | 4 |
| T5 | Documentar SLA de frescura en OpenAPI | DOCUMENTACION | Ana Paula | 2 |
| T6 | Peer review del monitor (sin abuso de recursos) | REVISION | Mateo | 2 |

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

### US-12 · Panel del docente con RLS y alerta de riesgo — 33 h

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

### US-13 · Indicadores consolidados con bloqueo de anonimato — 31 h

> **Rama:** `feature/us-13-kpis` · **Depende de:** US-11.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---:|---:|
| T1 | Servicio de agregación (CSAT 5 estrellas, engagement, tasas de aprobación/abandono) sobre los read models | BACKEND | Máximo | 6 |
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

---

## 4 · Resumen de carga por integrante

| Dev | Integrante | BACK | FRONT | TEST | REV | **Total** | Capacidad | % uso |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Luciano | — | 8 (US-10, US-14) | 6 (US-12) | 4 (US-04, US-13) | **18** | 39,4 | 46 % |
| 2 | Mateo | 8 (US-12, US-14) | 6 (US-13) | 2 (US-04) | 2 (US-10) | **18** | 41,5 | 43 % |
| 3 | Damian | 14 (US-05, US-11) | 6 (US-12) | — | — | **20** | 31,5 | 63 % |
| 4 | Joaquin | 10 (US-05, US-11) | — | 8 (US-04, US-10) | 2 (US-14) | **20** | 27,7 | 72 % |
| 6 | Valentina | 10 (US-05, US-10, US-12, US-13) | — | — | 2 (US-11) | **18** | 35,3 | 51 % |
| 7 | Máximo | 12 (US-04, US-13) | — | 14 (US-05, US-11, US-12, US-14) | — | **28** | 46,4 | 60 % |
| 8 | Regina | 14 (US-04, US-12, US-14) | 3 (US-05) | 6 (US-13) | — | **23** | 35,3 | 65 % |
| 9 | Bruno | 20 (US-04, US-10, US-11, US-13) | — | — | 4 (US-05, US-12) | **24** | 28,4 | 85 % |
| 10 | Ana Paula | — | — | — | — | **18 (DOC)** | 25,2 | MSII |

> **Total código: 169 h + 18 h DOC = 187 h · 65 % de 285,5 h.**
> **Frontend acotado** (4 tareas: US-05, US-10, US-12, US-13): el resto de la UI ya se entregó en S1 (slices 01–14).
> **Luciano** suma coordinación/ops (no cuenta como horas): compose, M2M T07, gateway/proxy y reviews de integración.
> **Bruno roza el 85 %** por el algoritmo de riesgo (US-11) — si sobra, se mueve la test de US-14 a Máximo.

---

## 5 · Secuencia de merges sugerida

| PR | Rama | Contenido | Merge |
|---|---:|---|---|
| #0 (cierre S1) | `#47`/`#48`/`#50` | topics T11 v3, placeholder ruta, contrato T01 | Día 1 |
| 1 | `feature/us-10-freshness` | monitor + `health/freshness` + badge | Día 2–3 |
| 2 | `feature/us-11-risk-readmodel` | migración + algoritmo + job | Día 4–5 |
| 3 | `feature/us-04-llm-facade` | cliente real T07 + catálogo | Día 5–6 |
| 4 | `feature/us-05-model-switch` | conmutación + evento | Día 7 |
| 5 | `feature/us-13-kpis` | indicadores + anonimato | Día 8 |
| 6 | `feature/us-12-teacher-panel` | panel + RLS (CRÍTICO) | Día 9 |
| 7 | `feature/us-14-thresholds` | umbrales + alertas | Día 10 |

> Los FE van en sus propias ramas (`feature/<us>-ui`) y se integran antes de cada merge de backend.
> **Regla:** nadie abre un PR sin `mvn clean verify` en verde (gate local, como S1); el revisor lo re-corre.

---

## 6 · Decisiones a confirmar en la planning

| # | Decisión | Recomiendo |
|---|---|---|
| D1 | ¿US-15 (reportes dinámicos, profe) entra este sprint? | No por capacidad; candidato Sprint 3 |
| D2 | ¿M2M real a T07 depende del token de T01? | Sí — Máximo lo desbloquea; hasta entonces el stub queda detrás del cliente real |
| D3 | ¿US-06/US-07 quedan bloqueadas? | Sí, hasta cerrar contrato de calibración con T07 |
| D4 | ¿Frontend alcanza con 4 tareas? | Sí, el resto ya está entregado |
| D5 | ¿Ana carga Taiga y documenta todo? | Sí (única con DOC) |

---

> **DoD:** el checklist unificado de `plan/sprint1/tareas/dev-*.md` aplica igual (tests, checkstyle, PMD, JaCoCo ≥ 90 %, commits en inglés).