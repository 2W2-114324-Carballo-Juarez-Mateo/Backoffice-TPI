# Tareas Sprint 2 — Backlog dividido (9 integrantes) · PROPUESTA UNIFICADA

> **Base:** propuesta de planning del grupo (Sprint 28/09 → 11/10/2026, 10 días hábiles) + ajustes del equipo: **US-15 (reportes docentes, requisito del profe) entra por fases**, **US-14 y calibración (HU06/HU07) pasan al Sprint 3**, regla de riesgo según `uh/US-11.md`.
> **Repos:** `2026-P4-BE/tpi-backoffice` · `2026-P4-FE/2026-PIV-TPI-FE`.
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` desde `develop` → PR a `develop` con ≥ 1 aprobación. Commits: backend en español (`AGENTS.md`), frontend en inglés.
> **Estimación:** historia = SP (Fibonacci) · tarea = horas. Las tareas con `#` ya existen en Taiga; `04-T1`, `15-T1`, etc. son nuevas.
> **Identificación:** el sprint `G06 - Sprint 2` lo crea alguien con permiso (la cátedra); la carga en Taiga se hace al aprobar el grupo.

---

## Decisiones del planning (ajustadas)

1. **Capacidad:** se mantiene la tabla del Sprint 1 (sin Julieta). **Decisión del equipo: sprint de MÁXIMA capacidad** — entra todo el backlog pendiente (HU04, HU05, HU06, HU07, HU08, HU11, HU12, HU13, HU14, US-15, deuda HT05/06/07, backfill). La carga nominal supera el 100 % por persona; el equipo asume entrega acelerada con IA y la regla de corte (§ corte) para lo que no alcance.
2. **Opción A (EP-03):** el Backoffice es **fachada del ADMIN sobre `/admin/*` de T07** — sin tablas LLM, sin calcular MAE. T07 decide `PASSED`/`FAILED`; **el Backoffice es dueño del valor de PAR-14**.
3. **HU06 (calibración) y HU07 (PAR-14/deriva):** entran al S2 como fachada sobre T07, **gated por C1** (si T07 no confirma `/api/llm/admin/*`, esas tareas quedan con stub + flag y se cortan).
4. **US-15 (reportes docentes, profe) entra completo:** fase 1 backend + **fase 2 (builder FE + specs)** en el mismo sprint.
5. **Regla de riesgo (HU11) — `uh/US-11.md` (la que tiene CA):**
   - **ROJO:** > 10 días de inactividad O reprobación > 60 %;
   - **AMARILLO:** 5–10 días, o reprobación 40–60 %;
   - **VERDE:** aprobación ≥ 70 %.
   - Umbrales en configuración tipada. "Vidas agotadas" detrás de un flag hasta que haya datos de T08.
6. **Historias Done con tareas reabiertas (#17, #28, #182)** → sus pendientes (#3331, #291, #3512) + #1657 pasan a **HT05** (deuda técnica frontend).
7. **HU09 (#20) → backlog (Could).** US-15 se crea en Taiga (historia + DoR) en la carga inicial.

---

## 1 · Capacidad

| Dev | Integrante | Rol | h/día | Días | Aus. | Teóricas | Efectivas | % | **Ajustada** | Plan | % uso |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Paz, Luciano | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 95 % | **39,4** | 50 | 127 % |
| 2 | Carballo Juarez, Mateo | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 100 % | **41,5** | 42 | 101 % |
| 3 | Baigorria, Damián ★ | PIV | 5 | 10 | 2 | 40 | 31,5 | 100 % | **31,5** | 41 | 130 % |
| 4 | Cortez, Joaquín | PIV | 5 | 10 | 2 | 40 | 31,5 | 88 % | **27,7** | 35 | 126 % |
| 6 | Maldonado, Valentina | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 85 % | **35,3** | 35 | 99 % |
| 7 | Cerquatti, Máximo ★ | MSII+PIV | 6 | 10 | 0 | 60 | 51,5 | 90 % | **46,4** | 50 | 108 % |
| 8 | Cerasulo, Regina ★ | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 85 % | **35,3** | 38 | 108 % |
| 9 | Gianoli, Bruno ★ | PIV | 4 | 10 | 0 | 40 | 31,5 | 90 % | **28,4** | 42 | 148 % |
| 10 | Ducart, Ana Paula | MSII | 5 | 10 | 2 | 40 | 31,5 | 80 % | **25,2** | 31 | 123 % |
| | **Total** | | | | **6** | **420** | **343,5** | | **310,6** | **364** | **117 %** |

> ⚠️ **Carga nominal 117 % — decisión del equipo de máxima capacidad (entrega acelerada con IA).** Si la realidad no acompaña, se corta en este orden: **flags (T10-1, B-AL, T08-1) → 10-M1 → HU13 (#322) → HU14 → HU06/07 (si C1 no llega, ya quedan con stub)**.

---

## 2 · Alcance (MoSCoW)

| Prioridad | Historia | Taiga | SP | Horas |
|---|---|---|---|---:|
| Must | HU04 · Proveedores y modelos (fachada T07) | #18 | 5 | 24 |
| Must | HU05 · Conmutación de modelos (fachada T07) | #26 | 5 | 21 |
| Must | HU06 · Calibración institucional (golden set) sobre T07 | #27 | 5 | 28 |
| Must | HU07 · PAR-14, veredicto y deriva sobre T07 | #29 | 5 | 16 |
| Must | HU08 · Ingesta: consumidores de T07 y T05 con flags | #1628 | 5 | 11 |
| Must | HT01 · Contratos entre temas | #1629 | 3 | 9 |
| Must | **HT05** · Frontend: acceso por rol, rutas `/backoffice` y deuda del S1 | nueva | 5 | 22 |
| Must | **HT06** · Backend: orden del outbox por `param_key` y confianza en el Gateway | nueva | 3 | 18 |
| Must | **HT07** · Documentación y proceso del Sprint 2 | nueva | 3 | 8 |
| Must | **US-15** · Reportes dinámicos (fase 1 backend + fase 2 builder FE) | nueva | 8 | 63 |
| Should | HU11 · Read model y riesgo por cohorte | #25 | 5 | 27 |
| Should | HU12 · Panel docente con RLS | #24 | 5 | 37 |
| Should | HU13 · Indicadores con anonimato | #111 | 5 | 26 |
| Should | HU14 · Umbrales de aviso | #34 | 3 | 22 |
| Should | **US-10 backfill** · Monitor `@Scheduled` de frescura | #28 (reabierta) | 3 | 8 |
| Should | **Alerta de presupuesto LLM (70 % → Backoffice, flag)** | nueva | 2 | 2 |
| Should | **Consumidor T10 (`sandbox.events`, flag)** | nueva | 2 | 4 |
| Should | **Ingesta T08 por REST (verificar/completar)** | nueva | 2 | 2 |
| Must | **HT07+ · Guion de la demo del S2 + E2E** | nueva | 2 | 3 |
| | **Total** | | **72** | **364** |
| Could | HU09 · Exportación | #20 | 5 | fuera |

**Por qué este corte:**
- **US-15 (profe)** entra por fases: el backend es la parte crítica (motor + RLS + whitelist) y ya se puede demostrar; el builder FE cierra en el S3.
- Las Must dejan la gobernanza de IA real (HU04/05) y la deuda del S1 (HT05/06/07) zanjada.
- HU11 → HU12 → HU13 es la cadena de valor de reporting; HU14 y la calibración (HU06/07) van al S3 por capacidad y por depender de C1/T07.
- **Velocidad:** HU04/05/08 y HT01 ya tienen la mayor parte de sus tareas cerradas; la medida real son las horas.

---

## 3 · EP-03 · Modelos LLM (fachada sobre T07) — HU04 y HU05 en S2

**Referencias:** Skill Hub `backoffice-admin-and-llm-service-integration-contract` v2 (T07) · `llm-service-http-contract` · `micro-to-micro-calls-with-a-service-token` (timeout 3 s, reintento solo en GET, propagar `X-Request-Id`, nunca traducir 401/403 de T07 a 500). Se reutilizan las interfaces congeladas `LlmAdminClient` y `LlmProviderClient` con sus stubs (`DefaultLlmAdminClient`, `DefaultLlmProviderClient`). **Antes de 04-T1 se necesita la respuesta a C1.**

### HU04 #18 — 24 h
> Ramas: `feature/tema-12-llm-admin-client` (04-T1, merge Día 3) y `feature/tema-12-llm-providers-models-real` (merge Día 5).

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| 04-T1 | Infraestructura del cliente HTTP hacia T07: `RestClient` administrado, auth de servicio según C1, `problem+json` → `LlmProviderException`/`LlmModelException`, URL base tipada; stub como fallback con flag | BACKEND | Máximo | 6 |
| 04-T2 | Cliente real de proveedores y credenciales (providers, provider-credentials, discover-models, test-model). La key viaja a T07; nunca se loguea ni se devuelve | BACKEND | Regina | 5 |
| 04-T3 | Conectar la pantalla 09 al backend (quitar los datos en memoria y pasar a `/api/backoffice/llm/...`) | FRONTEND | Regina | 4 |
| 04-T4 | Tests de integración con WireMock del cliente de proveedores: éxito, 404, 409, 503 y key enmascarada | TEST | Luciano | 5 |
| 04-T5 | Peer review de seguridad de credenciales y del cliente T07 | REVISION | Bruno | 2 |
| 04-T6 | OpenAPI de la fachada de proveedores y modelos | DOCUMENTACION | Regina | 2 |

### HU05 #26 — 21 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| 05-T1 | Cliente real de modelos evaluadores (listar, activo, desplegar, activar, borrar) sobre 04-T1 | BACKEND | Mateo | 5 |
| #276 | Modal de conmutación con advertencia (modelo actual → nuevo, confirmación explícita, textos de UI en español) | FRONTEND | Mateo | 3 |
| 05-T3 | Conectar la pantalla 10 al backend (quitar los datos en memoria) | FRONTEND | Mateo | 3 |
| 05-T4 | Tests con WireMock de la activación: 200, 409 (no aprobado) y 503 (T07 caído) | TEST | Luciano | 4 |
| 05-T5 | Contrato: `MODEL_CHANGED` lo publica T07 y el Backoffice deja de emitir `ModelProviderChanged` | DOCUMENTACION | Ana | 2 |
| 05-T6 | Peer review de la fachada de modelos (mapeo de errores, unicidad delegada en T07) | REVISION | Joaquín | 1 |
| 05-T7 | Specs de las pantallas 09 y 10 conectadas | TEST | Valentina | 3 |

> **HU06 y HU07 entran al Sprint 2** (fachada sobre T07, gated por C1). Si C1 no responde, quedan con stub + flag y son de las primeras en cortarse.

### HU06 #27 · Calibración institucional sobre T07 (fachada) — 28 h
> Rama: `feature/tema-12-calibration-facade` · merge Día 6.

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| #3537 | **Redefinida:** fachada del perfil de calibración institucional (golden set y rúbrica) sobre T07 | BACKEND | Joaquín | 4 |
| #3539 | **Redefinida:** fachada de corridas de calibración (crear, listar, detalle; `maeFinal`, `maxIndividualError` y veredicto de T07) | BACKEND | Bruno | 5 |
| #3538 | **Redefinida:** pantalla del perfil de calibración (parte 12) | FRONTEND | Joaquín | 5 |
| #3540 | **Redefinida:** pantalla de corridas de calibración (parte 13) | FRONTEND | Bruno | 5 |
| 06-T5 | Tests de integración con WireMock de la fachada de calibración | TEST | Máximo | 4 |
| 06-T6 | Diagrama de secuencia ADMIN → Backoffice → T07 (calibración) | DOCUMENTACION | Ana | 3 |
| 06-T7 | Peer review de la fachada de calibración | REVISION | Regina | 1 |
| 06-T8 | OpenAPI de la fachada de calibración | DOCUMENTACION | Joaquín | 1 |

### HU07 #29 · PAR-14, veredicto y deriva sobre T07 — 16 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| 07-T1 | PAR-14 como fuente de la tolerancia: validar rango y formato en el registro, y comprobar que T07 lo lea con `backoffice.parameters.read` | BACKEND | Damián | 3 |
| 07-T2 | Endpoint del estado de calibración del modelo activo (último veredicto y marca de deriva, leídos de T07) | BACKEND | Bruno | 4 |
| #284 | Indicador de veredicto y deriva, y banner de conmutación automática | FRONTEND | Valentina | 3 |
| 07-T4 | Tests del estado de calibración y de la validación de PAR-14 | TEST | Máximo | 3 |
| #286 | **Redefinida:** T07 calcula el MAE y el veredicto; el Backoffice gobierna PAR-14; la deriva la emite T07 | DOCUMENTACION | Bruno | 2 |
| 07-T6 | Peer review | REVISION | Mateo | 1 |

---

## 4 · EP-04 · Contratos e ingesta

### HU08 #1628 — 11 h
> Rama: `feature/tema-12-hu08-consumers` · merge Día 7. Los flags quedan apagados si el contrato no se confirma.

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| 08-T1 | Consumidores de `llm.events` (T07; filtrar por `eventType` según `llm-service-kafka-contract`) y de T05, detrás de flags, con deduplicación y DLT | BACKEND | Valentina | 5 |
| 08-T3 | Tests de integración: nuevo, duplicado, malformado → DLT, flag apagado | TEST | Bruno | 3 |
| 08-T4 | Actualizar el mapeo de contratos de lectura con T07 y T05 | DOCUMENTACION | Valentina | 2 |
| 08-T5 | Peer review de los consumidores | REVISION | Luciano | 1 |

### HT01 #1629 — 9 h · **se mandan el Día 1**

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| C1 | Confirmar con T07 y T01 la llamada a `/api/llm/admin/*`: ruta, Gateway o Eureka, token `client_credentials` o headers, y scopes | DOCUMENTACION | Máximo | 2 |
| C2 | Confirmar con T11 los topics de auditoría (`identity.audit` o `identity.audit.events`) y de notificaciones, y el `eventType` de `StudentAtHighRisk` | DOCUMENTACION | Mateo | 2 |
| C3 | Pedir a T02 la API de pertenencia docente por cohorte (HU12) y la fuente del CSAT (HU13) | DOCUMENTACION | Damián | 2 |
| C4 | Corregir el §6 del contrato de T07 (fachada) y registrar los acuerdos en `CONTRATOS.md` | DOCUMENTACION | Ana | 3 |

---

## 5 · Historias técnicas

### HT05 (nueva) · Frontend: acceso por rol, rutas `/backoffice` y deuda del Sprint 1 — 5 SP · 22 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| #3512 | Guards: pushear `ecae5fd`, abrir la PR y avisar a los dueños de las partes 03, 04, 06, 10 y 14 | FRONTEND | Máximo | 2 |
| #3331 | Solo lectura de parámetros para PROFESSOR (depende del permiso de lectura de T01) | FRONTEND | Bruno | 3 |
| #291 | Rehacer el badge de frescura revertido en la PR #99 | FRONTEND | Valentina | 3 |
| #1657 | Estado de 2FA y sesión (depende de T01) | FRONTEND | Regina | 3 |
| 05-N1 | Migrar las partes 01, 02, 04 y 06 de `/api/administration` y `/api/reports` a `/api/backoffice/...`, y retirar el parche de `proxy.conf.backoffice-gateway.cjs` | FRONTEND | Luciano | 4 |
| 05-N2 | Dashboard: ocultar los accesos no permitidos a GESTOR y PROFESSOR | FRONTEND | Valentina | 2 |
| 05-N3 | Specs de guards y de la vista de solo lectura (ADMIN, GESTOR y PROFESSOR) | TEST | Mateo | 3 |
| 05-N4 | Peer review de guards, migración de rutas, badge y 2FA | REVISION | Joaquín | 2 |

### HT06 (nueva) · Backend: orden del outbox y confianza en el Gateway — 3 SP · 18 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| 06B-T1 | Orden estricto por `param_key` en `OutboxMessageRepository.findReadyToPublish`: no tomar una fila si hay una `PENDING` más vieja de la misma key (US-02 CA4; detalle en la PR #46) | BACKEND | Luciano | 4 |
| 06B-T2 | Test de integración del orden: falla v1 y v2 no sale antes (Testcontainers con Kafka) | TEST | Máximo | 4 |
| 06B-T3 | Verificar `GATEWAY_SHARED_SECRET` (`GatewayTrustProperties`) con el mecanismo que acuerde T01, sin romper el entorno local | BACKEND | Máximo | 3 |
| 06B-T4 | Configurar el topic de auditoría confirmado (C2) y la auditoría delegada en T01 (pantalla 11 sin 502) | BACKEND | Máximo | 2 |
| 06B-T5 | Peer review de concurrencia del outbox y del secreto del Gateway | REVISION | Valentina | 2 |
| 06B-T6 | Documentar el orden por key en el contrato del consumidor | DOCUMENTACION | Luciano | 1 |
| 06B-T7 | Peer review de las PRs abiertas del backend #47, #48 y #50 | REVISION | Luciano | 2 |

### HT07 (nueva) · Documentación y proceso del Sprint 2 — 3 SP · 8 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| D1 | Diagrama de secuencia del cambio de parámetro (diferido del Sprint 1) | DOCUMENTACION | Ana | 3 |
| D3 | Sincronizar `docs/backend/docs` y el sitio con lo real: fachada T07, auditoría vía T01, `/backoffice`, roles del Gateway v3 | DOCUMENTACION | Ana | 3 |
| D4 | Carga y sincronización de Taiga del Sprint 2 y acta de la retro del Sprint 1 | DOCUMENTACION | Ana | 2 |

---

## 6 · US-15 (nueva) · Reportes docentes dinámicos (completo: fase 1 backend + fase 2 builder FE) — 8 SP · 63 h · 🔴 P0 (requisito del profe)

> **Rama:** `feature/tema-12-us15-dynamic-reports` · **Depende de:** HU11 (read models) + pertenencia T02 + anti-comparación.
> El PROFESOR arma reportes eligiendo **métricas, filtros, período, columnas y agrupación** y **guarda** la configuración (plantilla).

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| 15-T1 | Catálogo de métricas permitidas (whitelist): CSAT, engagement, aprobación/abandono, promoción, riesgo, distribución XP, actividad semanal — **sin expresiones arbitrarias** | BACKEND | Joaquín | 8 |
| 15-T2 | Motor de query dinámico (filtros/período/columnas/agrupación) sobre read models con **RLS por `course_id`** | BACKEND | Bruno | 12 |
| 15-T3 | CRUD de plantillas y favoritas (`report_template(owner_id, course_id, config jsonb, is_favorite)`) | BACKEND | Joaquín | 8 |
| 15-T4 | Ejecución `POST /api/backoffice/reports/run` (con `templateId` o `config`) + invariantes: matrícula T02, anti-comparación (RF-RPT-07), anonimato (encuestas solo agregados), frescura ≤ 15 min | BACKEND | Mateo | 8 |
| 15-T5 | Tests del motor dinámico: RLS (PROFESOR A → cohorte A 200 · cohorte B 403), anti-comparación, anonimato | TEST | Máximo | 8 |
| 15-T6 | OpenAPI de templates/run + catálogo de métricas | DOCUMENTACION | Ana | 4 |
| 15-T7 | Peer review de seguridad del motor (RLS/whitelist) | REVISION | Valentina | 3 |
| 15-T8 | **Fase 2:** FE report builder — panel métricas/filtros/período/columnas/agrupación + "Guardar plantilla" (WCAG AA) | FRONTEND | Luciano | 12 |
| 15-T9 | **Fase 2:** specs del builder (crear/editar/correr plantilla, validaciones, 403 no-ADMIN/gestor) | TEST | Damián | 8 |
| 15-T10 | **Fase 2:** OpenAPI + documentación de la vista builder | DOCUMENTACION | Ana | 3 |
| 15-T11 | **Fase 2:** peer review del builder | REVISION | Regina | 2 |

> **Reserva Flyway:** US-15 usa `V20__reporting_custom_templates.sql` (Joaquín).

---

## 7 · EP-05 · Analítica (reporting)

### HU11 #25 — 27 h · regla de riesgo `uh/US-11.md`
> Ramas: `feature/tema-12-hu11-read-model` (solo V18, merge Día 3) y `feature/tema-12-hu11-risk` (merge Día 6).

| # | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| #303 | Migración `V18__reporting_cohort_student_summary` y read model con índices | BACKEND | Damián | 6 |
| #304 | Algoritmo de riesgo con la regla de `uh/US-11.md` (ROJO >10 días o >60 % · AMARILLO 5–10 días o 40–60 % · VERDE ≥70 %); umbrales en configuración tipada; "vidas agotadas" detrás de flag | BACKEND | Damián | 6 |
| #305 | Job programado de recálculo desde `ingested_event` (T03 `challenge.events` y T02 `course.events`) | BACKEND | Valentina | 5 |
| #306 | Partición de equivalencia y valores límite: 10/11 días, 4/5 días, 40/60 %, 70 % | TEST | Regina | 5 |
| #307 | Reglas de riesgo y esquema del read model | DOCUMENTACION | Mateo | 3 |
| #308 | Peer review del modelado analítico | REVISION | Máximo | 2 |

### HU12 #24 — 37 h · DoD con **RLS verificado**
> Rama: `feature/tema-12-hu12-teacher-panel` · merge Día 8.

| # | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| #310 | `GET ${app.api.private-path}/reports/courses/{courseId}/teacher` con un puerto de pertenencia docente (adaptador de T02 según C3; si no hay respuesta, flag) | BACKEND | Regina | 6 |
| #311 | RLS por `course_id` (`app.current_course`; `ALL` solo para ADMIN) y regla anti-comparación, migración `V19` | BACKEND | Máximo | 6 |
| #312 | `StudentAtHighRisk` por outbox al pasar a ROJO (topic según C2) | BACKEND | Luciano | 4 |
| #313 | Panel docente con semáforo (color y texto, WCAG AA) | FRONTEND | Damián | 5 |
| #314 | Tests de RLS: A → A 200, A → B 403, `ALL` 403, ADMIN 200 (Testcontainers con PostgreSQL) | TEST | Luciano | 5 |
| #315 | Tests de anti-comparación y de emisión del evento | TEST | Damián | 4 |
| 12-T9 | Specs del panel docente | TEST | Joaquín | 2 |
| #316 | Endpoints, política RLS y contrato de la alerta | DOCUMENTACION | Ana | 3 |
| #317 | Peer review de seguridad RLS (crítico) | REVISION | Mateo | 2 |

### HU13 #111 — 26 h
> Rama: `feature/tema-12-hu13-indicators` · merge Día 8. **Si el sprint se atrasa, es la primera que se corta.**

| # | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| #319 | Agregación de indicadores: aprobación, abandono y actividad semanal; CSAT solo si hay datos | BACKEND | Mateo | 6 |
| #320 | Anonimato por umbral mínimo, en configuración (verificar en `PARAMETROS.md` si corresponde a un PAR: hay conflicto con PAR-18) | BACKEND | Regina | 4 |
| #321 | `GET ${app.api.private-path}/reports/platform`, solo ADMIN, sin ranking | BACKEND | Bruno | 4 |
| #322 | Dashboard de KPIs con aviso de "muestra insuficiente" | FRONTEND | Valentina | 5 |
| #323 | Tests de anonimato (4, 5 y 6 respuestas) y 403 para quien no es ADMIN | TEST | Máximo | 4 |
| #324 | Políticas de privacidad y fórmulas | DOCUMENTACION | Joaquín | 2 |
| #325 | Peer review de privacidad | REVISION | Damián | 1 |

### HU14 #34 · Umbrales de aviso y acceso al tablero — 22 h
> Rama: `feature/tema-12-hu14-thresholds` · merge Día 9 · **Depende de:** HU13 (indicadores calculados).

| # | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| #326 | Modelo `alert_thresholds(indicator, min_value, max_value, enabled)` + CRUD exclusivo ADMIN | BACKEND | Regina | 4 |
| #330 | Evaluador periódico de métricas contra umbrales + `ThresholdBreached` (baja → alerta) | BACKEND | Mateo | 6 |
| 14-T3 | FE: panel de configuración de umbrales + lista de alertas activas | FRONTEND | Luciano | 4 |
| 14-T4 | Tests: evaluación en el límite y 1 punto abajo + no-ADMIN 403 | TEST | Máximo | 4 |
| 14-T5 | Documentar catálogo de umbrales + evento `ThresholdBreached` | DOCUMENTACION | Ana | 2 |
| 14-T6 | Peer review de consistencia del backlog de reporting | REVISION | Joaquín | 2 |

---

## 7bis · Pendientes adicionales (backfill — no queda trabajo sin listar) — 19 h

> Tareas que quedaban **sin registrar en ninguna tabla** y ahora entran al Sprint 2. Las que dependen de un contrato externo van **detrás de flags** (si el contrato no llega, no arrancan y se cortan primero).

| ID | Tarea | Tipo | Dev | h | Gate |
|---|---|---|---:|---|
| 10-M1 | **US-10 backfill:** monitor `@Scheduled` que marca `isStale` en `IngestionCounter` cuando `now − last_event_at > PAR-23` (15 min) y lo normaliza al llegar datos | BACKEND | Damián | 6 | — |
| 10-M2 | Tests del monitor: 16 min → stale, dato llega → marca retirada | TEST | Regina | 2 | — |
| B-AL | **Alerta de presupuesto LLM (70 % → Backoffice):** consumidor de `llm.budget.events` (T07) detrás de flag; `LLMBudgetAlert` → notificación (revisar `CONTRATOS.md` T07) | BACKEND | Valentina | 2 | 🔶 T07 |
| T10-1 | **Consumidor de T10 (`sandbox.events`):** dedup + read model de progreso/niveles, detrás de flag (contrato con T10) | BACKEND | Luciano | 4 | 🔶 T10 |
| T08-1 | **Ingesta T08 por REST (`/api/bank/**`):** verificar/completar el adapter de replay + evento de saldo | BACKEND | Bruno | 2 | 🔶 T08 |
| D-DEMO | **Guion de la demo del S2 + checklist E2E** (reportes dinámicos + fachada LLM + panel docente) | DOCUMENTACION | Ana | 3 | — |

> **Regla de corte (en orden):** primero se cortan `T10-1`, `B-AL` y `T08-1` (flags apagados) → luego `10-M1/M2` → luego HU13 (#322).

---

## 8 · Matriz de revisión cruzada (nadie testea ni revisa lo suyo)

| Código de… | Lo testea | Lo revisa |
|---|---|---|
| 04-T1, cliente T07 (Máximo) | Luciano (04-T4 y 05-T4) | Bruno (04-T5) |
| 04-T2/04-T3, proveedores (Regina) | Luciano (04-T4) · Valentina (05-T7) | Bruno (04-T5) |
| 05-T1, #276 y 05-T3, modelos (Mateo) | Luciano (05-T4) · Valentina (05-T7) | Joaquín (05-T6) |
| 08-T1, consumidores (Valentina) | Bruno (08-T3) | Luciano (08-T5) |
| #3512 (Máximo) · #3331 (Bruno) · #291/05-N2 (Valentina) · #1657 (Regina) · 05-N1 (Luciano) | Mateo (05-N3) | Joaquín (05-N4) |
| 06B-T1 (Luciano) · 06B-T3/T4 (Máximo) | Máximo (06B-T2, sobre código de Luciano) | Valentina (06B-T5) |
| PRs #47, #48 y #50 (Mateo) | — | Luciano (06B-T7) |
| **15-T1/15-T3 (Joaquín) · 15-T2 (Bruno) · 15-T4 (Mateo)** | **Máximo (15-T5)** | **Valentina (15-T7)** |
| #303/#304 (Damián) · #305 (Valentina) | Regina (#306) | Máximo (#308) |
| #310 (Regina) · #311 (Máximo) · #312 (Luciano) · #313 (Damián) | Luciano (#314, sobre #310/#311) · Damián (#315, sobre #311/#312) · Joaquín (12-T9, sobre #313) | Mateo (#317) |
| #319/#321 (Mateo/Bruno) · #320 (Regina) · #322 (Valentina) | Máximo (#323) | Damián (#325) |
| 10-M1 (Damián) | Regina (10-M2) | PR normal (Luciano) |
| B-AL (Valentina) | — | Luciano |
| T10-1 (Luciano) | — | Joaquín |
| T08-1 (Bruno) | — | Regina |
| D-DEMO (Ana) | — | Luciano (exactitud técnica) |
| #3537/#3538 (Joaquín) · #3539/#3540 (Bruno) | Máximo (06-T5) | Regina (06-T7) |
| 07-T1 (Damián) · 07-T2/#286 (Bruno) · #284 (Valentina) | Máximo (07-T4) | Mateo (07-T6) |
| #326 (Regina) · #330 (Mateo) · 14-T3 (Luciano) | Máximo (14-T4) | Joaquín (14-T6) |
| 15-T8 (Luciano) | Damián (15-T9) | Regina (15-T11) |

---

## 9 · Resumen de carga (los 8 que codifican cubren las 5 capas)

| Dev | Integrante | Capacidad | Horas | % uso | BACK | FRONT | TEST | REV | DOC |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Luciano Paz | 39,4 | 50 | 127 % | 16 | 16 | 14 | 3 | 1 |
| 2 | Mateo Carballo Juarez | 41,5 | 42 | 101 % | 24 | 6 | 3 | 3 | 6 |
| 3 | Damián Baigorria ★ | 31,5 | 41 | 130 % | 23 | 5 | 12 | 1 | — |
| 4 | Joaquín Cortez | 27,7 | 35 | 126 % | 20 | 5 | 2 | 4 | 4 |
| 6 | Valentina Maldonado | 35,3 | 35 | 99 % | 12 | 15 | 3 | 3 | 2 |
| 7 | Máximo Cerquatti ★ | 46,4 | 50 | 108 % | 19 | 2 | 27 | 2 | — |
| 8 | Regina Cerasulo ★ | 35,3 | 38 | 108 % | 19 | 7 | 7 | 3 | 2 |
| 9 | Bruno Gianoli ★ | 28,4 | 42 | 148 % | 30 | 5 | 3 | 2 | 2 |
| 10 | Ana Paula Ducart | 25,2 | 31 | 123 % | — | — | — | — | 31 |
| | **Total** | **310,6** | **364** | **117 %** | | | | | |

> **Sprint de máxima capacidad:** entra todo el backlog pendiente (incluye HU06, HU07, HU14 y US-15 fase 2). La carga nominal supera la capacidad — asumido por el equipo (IA acelerada). **Orden de corte si la realidad no acompaña:** flags (T10-1, B-AL, T08-1) → 10-M1 → HU13 (#322) → HU14 → HU06/07 (si C1 no llega, quedan con stub).

---

## 10 · Ruta crítica y priorización

| # | Punto crítico | Mitigación |
|---|---|---|
| PC1 | **15-T2 (motor dinámico, Bruno) es la ruta crítica de US-15** — de él dependen los tests de Máximo (15-T5) y el builder de Luciano (15-T8) | **Bruno arranca con 15-T2 el Día 1.** Si el motor se demora, **#3540 (pantalla de corridas) y T08-1 se postergan** o se apoyan en otro dev |
| PC2 | **HU04/05/06/07 dependen de la API de T07** | **Máximo arma 04-T1 con WireMock desde el Día 1** (no espera C1). Si T07 no entrega el contrato, las historias quedan **funcionales con stubs/mocks bajo flag** y la demo usa el stub |
| PC3 | **Migraciones Flyway concurrentes** | Versiones + dueños reservados (§ secuencia de merges): **V18 → Damián · V19 → Máximo · V20 → Joaquín** |

---

## 11 · Secuencia de merges

**Día 1:** se mandan C1, C2 y C3. Las interfaces LLM ya están congeladas, así que Regina, Mateo, Bruno y Joaquín arrancan con mocks.
**Versiones de Flyway reservadas (dueño):** **V18** `reporting_cohort_student_summary` → **Damián** (HU11) · **V19** `reporting_teacher_course_rls` → **Máximo** (HU12 RLS) · **V20** `reporting_custom_templates` → **Joaquín** (US-15). Nadie usa otro número sin avisar.

| Día | Backend (PR a `develop`) | Frontend |
|---|---|---|
| 2 | — | #3512 guards |
| 3 | `fix/tema-12-outbox-key-ordering` (HT06) · `feature/tema-12-llm-admin-client` (04-T1) · `feature/tema-12-hu11-read-model` (V18) | 05-N1, migración a `/backoffice` |
| 5 | `feature/tema-12-llm-providers-models-real` (HU04) | integración `feature/tema-12-backoffice` → `develop` (Luciano) |
| 6 | `feature/tema-12-hu11-risk` · `feature/tema-12-calibration-facade` (HU06) · `feature/tema-12-us15-dynamic-reports` (base V18) | pantallas 09 y 10 conectadas, #276, #284 |
| 7 | `feature/tema-12-hu08-consumers` · `fix/tema-12-gateway-trust` (si T01 respondió) | #291, 05-N2 |
| 8 | `feature/tema-12-hu12-teacher-panel` (V19) · `feature/tema-12-hu13-indicators` · `feature/tema-12-hu07-verdict-drift` (HU07, si C1) | #3331 y #1657 (si T01 respondió) · #313, #322 · partes 12 y 13 (HU06) |
| 9 | `feature/tema-12-hu14-thresholds` · **10-M1 monitor** · US-15 `run` (V20) | #313, #322, builder US-15 (15-T8) |
| 10–11 | ajustes y cierre · **flags T10-1/B-AL/T08-1 (solo si llegó el contrato)** | integración final + demo |

---

## 12 · Riesgos y dependencias externas

| # | Equipo | Dependencia | Bloquea | Mitigación |
|---|---|---|---|---|
| R1 | **T07** | Ruta, autenticación y scopes de `/api/llm/admin/*` (contradicción handoff "sin M2M/Eureka" vs skill "token propio + Gateway") | 04-T2, 05-T1, **HU06, HU07**, 15-T2 | **Máximo arma 04-T1 con WireMock desde el Día 1 (no espera C1).** Si T07 no responde el Día 5, HU04–07 quedan **funcionales con stubs/mocks bajo flag** y la demo usa el stub |
| R2 | **T01** | Permiso de lectura para PROFESSOR, estado de 2FA, `GET /api/users/audit`, secreto del Gateway, token de servicio | #3331, #1657, 06B-T3/T4, pantalla 11 | Tareas aisladas; si no hay respuesta el Día 6, pasan a "Necesita información" |
| R3 | **T11** | Topics de auditoría y notificaciones, `eventType` de `StudentAtHighRisk` | #312, 06B-T4 | El topic va en configuración; el outbox guarda igual |
| R4 | T02 | Pertenencia docente y fuente del CSAT | #310, #319 | Puerto con flag; el CSAT muestra "muestra insuficiente" |
| R5 | T05 | Topic de entregas (G2) | 08-T1 | Flag apagado y documentado |
| R6 | **T10** | Topic `sandbox.events` y formato del read model | T10-1 | Flag apagado; si no responde, T10-1 se corta |
| R7 | **T07** | Topic `llm.budget.events` y payload de `LLMBudgetAlert` | B-AL | Flag apagado; se corta si no llega |
| R8 | **T08** | Replay REST `/api/bank/**` y evento de saldo | T08-1 | Verificación aislada; se corta si no confirma |
| I1 | Interno | **Carga nominal 117 %** (sprint de máxima capacidad) | Todo el sprint | **Corte primero:** flags (T10-1, B-AL, T08-1) → 10-M1 → HU13 (#322) → HU14 → HU06/07 (si C1) |
| I2 | Interno | Colisión de versiones de Flyway | Todas las migraciones | Versiones reservadas V18/V19/V20 (§10) |
| I3 | Interno | Archivos protegidos del frontend (`angular.json`, `package*.json`, `tsconfig*`) | Todas las PRs del frontend | No tocarlos |

---

## 13 · DoD (igual para los 9)

- **Nivel 0 · Tarea:** `mvn -B verify` en verde (tests, Checkstyle, **PMD 3.26**, **JaCoCo ≥ 0,90**); en el frontend, `npm run verify` (lint:all y Vitest) **sin `ng build` local**.
- **Nivel 1 · Historia:** cumple los CA; integración con Testcontainers donde haya eventos o BD; autorización 200/403 con roles del Gateway v3 (`ADMIN`, `GESTOR`, `PROFESSOR`, `STUDENT`, `MS`); **RLS verificado** en HU11/12/13 y US-15; OpenAPI actualizado; PR revisada por otra persona; sin secretos; docs y sitio sincronizados (D3).
- **Nivel 2 · Sprint:** suite completa en verde, Taiga al día, demo y retro.
- **Skills del Skill Hub:** `project-quality-gate` · `project-gitflow-guard` · `micro-to-micro-calls-with-a-service-token` (cliente T07) · `typed-configuration-env-vars-and-secret-files` (umbrales y flags) · `frontend-ui-kit-compliance` · `frontend-through-the-api-gateway`.

---

## 14 · Fuera del Sprint 2 (solo Could)

| Historia | Horas | Nota |
|---|---:|---|
| HU09 · Exportación asíncrona PDF/CSV | Could | La UI de export (slice 07) ya existe; el backend asíncrono queda fuera por ser Could |

> **Todo el resto del backlog (US-04/05/06/07/08/10/11/12/13/14/15 + deuda + backfill) está en el Sprint 2.** Solo HU09 queda como Could fuera del sprint.