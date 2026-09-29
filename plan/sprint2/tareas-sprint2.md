# Tareas Sprint 2 — Backlog dividido (9 integrantes) · PROPUESTA UNIFICADA

> **Base:** propuesta de planning del grupo (Sprint 28/09 → 11/10/2026, 10 días hábiles) + ajustes del equipo: **US-15 (reportes docentes, requisito del profe) entra por fases**, **US-14 y calibración (HU06/HU07) pasan al Sprint 3**, regla de riesgo según `uh/US-11.md`.
> **Repos:** `2026-P4-BE/tpi-backoffice` · `2026-P4-FE/2026-PIV-TPI-FE`.
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` desde `develop` → PR a `develop` con ≥ 1 aprobación. Commits: backend en español (`AGENTS.md`), frontend en inglés.
> **Estimación:** historia = SP (Fibonacci) · tarea = horas. Las tareas con `#` ya existen en Taiga; `04-T1`, `15-T1`, etc. son nuevas.
> **Identificación:** el sprint `G06 - Sprint 2` lo crea alguien con permiso (la cátedra); la carga en Taiga se hace al aprobar el grupo.

---

## Decisiones del planning (ajustadas)

1. **Capacidad:** se mantiene la tabla del Sprint 1 (sin Julieta). **Pedido del PO:** más carga para **Máximo, Regina, Damián y Bruno**.
2. **Opción A (EP-03):** el Backoffice es **fachada del ADMIN sobre `/admin/*` de T07** — sin tablas LLM, sin calcular MAE. T07 decide `PASSED`/`FAILED`; **el Backoffice es dueño del valor de PAR-14**.
3. **Corte de alcance por capacidad (con US-15):**
   - **US-15 (reportes docentes dinámicos) entra en S2 por fases:** backend (whitelist + motor + plantillas + `run`) en S2; **builder FE + tests del builder → Sprint 3**.
   - **HU14 (umbrales, #34) → Sprint 3** (Should).
   - **HU06 (calibración #27) y HU07 (PAR-14/deriva #29) → Sprint 3** como fachada, **gated por C1** (respuesta de T07 a `/api/llm/admin/*`). Liberan ~44 h para US-15.
4. **Regla de riesgo (HU11) — `uh/US-11.md` (la que tiene CA):**
   - **ROJO:** > 10 días de inactividad O reprobación > 60 %;
   - **AMARILLO:** 5–10 días, o reprobación 40–60 %;
   - **VERDE:** aprobación ≥ 70 %.
   - Umbrales en configuración tipada. "Vidas agotadas" detrás de un flag hasta que haya datos de T08.
5. **Historias Done con tareas reabiertas (#17, #28, #182)** → sus pendientes (#3331, #291, #3512) + #1657 pasan a **HT05** (deuda técnica frontend).
6. **HU09 (#20) → backlog (Could).** Sin US-15 en Taiga aún: se crea (historia + DoR) en la carga inicial.

---

## 1 · Capacidad

| Dev | Integrante | Rol | h/día | Días | Aus. | Teóricas | Efectivas | % | **Ajustada** | Plan | % uso |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Paz, Luciano | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 95 % | **39,4** | 34 | 86 % |
| 2 | Carballo Juarez, Mateo | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 100 % | **41,5** | 35 | 84 % |
| 3 | Baigorria, Damián ★ | PIV | 5 | 10 | 2 | 40 | 31,5 | 100 % | **31,5** | 30 | 95 % |
| 4 | Cortez, Joaquín | PIV | 5 | 10 | 2 | 40 | 31,5 | 88 % | **27,7** | 23 | 83 % |
| 6 | Maldonado, Valentina | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 85 % | **35,3** | 32 | 91 % |
| 7 | Cerquatti, Máximo ★ | MSII+PIV | 6 | 10 | 0 | 60 | 51,5 | 90 % | **46,4** | 39 | 84 % |
| 8 | Cerasulo, Regina ★ | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 85 % | **35,3** | 31 | 88 % |
| 9 | Gianoli, Bruno ★ | PIV | 4 | 10 | 0 | 40 | 31,5 | 90 % | **28,4** | 26 | 92 % |
| 10 | Ducart, Ana Paula | MSII | 5 | 10 | 2 | 40 | 31,5 | 80 % | **25,2** | 23 | 91 % |
| | **Total** | | | | **6** | **420** | **343,5** | | **310,6** | **273** | **88 %** |

> ★ **Foco del PO:** Máximo, Regina, Damián y Bruno 84–95 %. **273 h = 88 %** es alto (sin margen): **todas las tareas con flag (T10, presupuesto LLM, T08) y el monitor de frescura son las primeras que se cortan** si el sprint se atrasa; HU13 (#322) le sigue.

---

## 2 · Alcance (MoSCoW)

| Prioridad | Historia | Taiga | SP | Horas |
|---|---|---|---|---:|
| Must | HU04 · Proveedores y modelos (fachada T07) | #18 | 5 | 24 |
| Must | HU05 · Conmutación de modelos (fachada T07) | #26 | 5 | 21 |
| Must | HU08 · Ingesta: consumidores de T07 y T05 con flags | #1628 | 5 | 11 |
| Must | HT01 · Contratos entre temas | #1629 | 3 | 9 |
| Must | **HT05** · Frontend: acceso por rol, rutas `/backoffice` y deuda del S1 | nueva | 5 | 22 |
| Must | **HT06** · Backend: orden del outbox por `param_key` y confianza en el Gateway | nueva | 3 | 18 |
| Must | **HT07** · Documentación y proceso del Sprint 2 | nueva | 3 | 8 |
| Must | **US-15 (fase 1)** · Reportes dinámicos: **backend** (whitelist + motor + plantillas + `run`) | nueva | 5 | 36 |
| Should | HU11 · Read model y riesgo por cohorte | #25 | 5 | 27 |
| Should | HU12 · Panel docente con RLS | #24 | 5 | 37 |
| Should | HU13 · Indicadores con anonimato | #111 | 5 | 26 |
| Should | **US-10 backfill · Monitor `@Scheduled` de frescura** | #28 (reabierta) | 3 | 8 |
| Should | **Alerta de presupuesto LLM (70 % → Backoffice, flag)** | nueva | 2 | 2 |
| Should | **Consumidor T10 (`sandbox.events`, flag)** | nueva | 2 | 4 |
| Should | **Ingesta T08 por REST (verificar/completar)** | nueva | 2 | 2 |
| Must | **HT07+ · Guion de la demo del S2 + E2E** | nueva | 2 | 3 |
| | **Total** | | **58** | **273** |
| → Sprint 3 | HU06 · Calibración sobre T07 (gated C1) | #27 | 5 | 28 |
| → Sprint 3 | HU07 · PAR-14, veredicto y deriva sobre T07 (gated C1) | #29 | 5 | 16 |
| → Sprint 3 | HU14 · Umbrales y alertas | #34 | 3 | 22 |
| → Sprint 3 | US-15 fase 2 · Builder FE + tests del builder | nueva | 5 | ~27 |
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

> **HU06 (calibración #27) y HU07 (PAR-14/deriva #29) → Sprint 3** (fachada sobre T07, gated por C1). Las tareas redefinidas #3537–#3540 y #286 quedan en backlog S3.

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

## 6 · US-15 (nueva) · Reportes docentes dinámicos — **fase 1 backend** — 5 SP · 36 h · 🔴 P0 (requisito del profe)

> **Rama:** `feature/tema-12-us15-dynamic-reports` · **Depende de:** HU11 (read models) + pertenencia T02 + anti-comparación.
> El PROFESOR arma reportes eligiendo **métricas, filtros, período, columnas y agrupación** y **guarda** la configuración (plantilla).
> **Fase 2 (Sprint 3):** builder FE + specs del builder (~27 h).

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---:|
| 15-T1 | Catálogo de métricas permitidas (whitelist): CSAT, engagement, aprobación/abandono, promoción, riesgo, distribución XP, actividad semanal — **sin expresiones arbitrarias** | BACKEND | Joaquín | 8 |
| 15-T2 | Motor de query dinámico (filtros/período/columnas/agrupación) sobre read models con **RLS por `course_id`** | BACKEND | Bruno | 12 |
| 15-T3 | CRUD de plantillas y favoritas (`report_template(owner_id, course_id, config jsonb, is_favorite)`) | BACKEND | Joaquín | 8 |
| 15-T4 | Ejecución `POST /api/backoffice/reports/run` (con `templateId` o `config`) + invariantes: matrícula T02, anti-comparación (RF-RPT-07), anonimato (encuestas solo agregados), frescura ≤ 15 min | BACKEND | Mateo | 8 |
| 15-T5 | Tests del motor dinámico: RLS (PROFESOR A → cohorte A 200 · cohorte B 403), anti-comparación, anonimato | TEST | Máximo | 8 |
| 15-T6 | OpenAPI de templates/run + catálogo de métricas | DOCUMENTACION | Ana | 4 |
| 15-T7 | Peer review de seguridad del motor (RLS/whitelist) | REVISION | Valentina | 3 |

> **Reserva Flyway:** US-15 usa `V20__report_template.sql`.

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

---

## 9 · Resumen de carga (los 8 que codifican cubren las 5 capas)

| Dev | Integrante | Capacidad | Horas | % uso | BACK | FRONT | TEST | REV | DOC |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Luciano Paz | 39,4 | 34 | 86 % | 12 | 4 | 14 | 3 | 1 |
| 2 | Mateo Carballo Juarez | 41,5 | 35 | 84 % | 18 | 6 | 3 | 2 | 6 |
| 3 | Damián Baigorria ★ | 31,5 | 30 | 95 % | 20 | 5 | 4 | 1 | — |
| 4 | Joaquín Cortez | 27,7 | 23 | 83 % | 16 | — | 2 | 4 | 2 |
| 6 | Valentina Maldonado | 35,3 | 32 | 91 % | 12 | 12 | 3 | 3 | 2 |
| 7 | Máximo Cerquatti ★ | 46,4 | 39 | 84 % | 19 | 2 | 16 | 2 | — |
| 8 | Regina Cerasulo ★ | 35,3 | 31 | 88 % | 15 | 7 | 7 | — | 2 |
| 9 | Bruno Gianoli ★ | 28,4 | 26 | 92 % | 21 | — | 3 | 2 | — |
| 10 | Ana Paula Ducart | 25,2 | 23 | 91 % | — | — | — | — | 23 |
| | **Total** | **310,6** | **273** | **88 %** | | | | | |

> **Cambios vs la propuesta unificada anterior:** se sumaron los pendientes que no estaban en ninguna tabla: monitor de frescura (US-10, 8 h), alerta de presupuesto LLM (2 h, flag), consumidor T10 (4 h, flag), ingesta T08 REST (2 h, flag) y guion de demo S2 (3 h). **Con esto, el backlog del S2 no deja trabajo sin listar** (lo que no entra está en Sprint 3 o es Could/flag).

---

## 10 · Secuencia de merges

**Día 1:** se mandan C1, C2 y C3. Las interfaces LLM ya están congeladas, así que Regina, Mateo, Bruno y Joaquín arrancan con mocks.
**Versiones de Flyway reservadas:** **V18** (HU11) · **V19** (HU12 RLS) · **V20** (US-15 plantillas). Nadie usa otro número sin avisar.

| Día | Backend (PR a `develop`) | Frontend |
|---|---|---|
| 2 | — | #3512 guards |
| 3 | `fix/tema-12-outbox-key-ordering` (HT06) · `feature/tema-12-llm-admin-client` (04-T1) · `feature/tema-12-hu11-read-model` (V18) | 05-N1, migración a `/backoffice` |
| 5 | `feature/tema-12-llm-providers-models-real` (HU04) | integración `feature/tema-12-backoffice` → `develop` (Luciano) |
| 6 | `feature/tema-12-hu11-risk` · `feature/tema-12-us15-dynamic-reports` (base V18) | pantallas 09 y 10 conectadas, #276 |
| 7 | `feature/tema-12-hu08-consumers` · `fix/tema-12-gateway-trust` (si T01 respondió) | #291, 05-N2 |
| 8 | `feature/tema-12-hu12-teacher-panel` (V19) · `feature/tema-12-hu13-indicators` | #3331 y #1657 (si T01 respondió) · #313, #322 |
| 9–10 | ajustes y cierre · US-15 `run` (V20) · **10-M1 monitor (si sobra margen)** · **flags T10-1/B-AL/T08-1 (solo si llegó el contrato)** | integración final |

> **Backfill (Día 9–10, solo si el margen lo permite):** `feature/tema-12-freshness-monitor` (10-M1) · `feature/tema-12-hu08-extra-consumers` (T10-1, B-AL, T08-1 detrás de flags). Primero en cortarse.

---

## 11 · Riesgos y dependencias externas

| # | Equipo | Dependencia | Bloquea | Mitigación |
|---|---|---|---|---|
| R1 | **T07** | Ruta, autenticación y scopes de `/api/llm/admin/*` (contradicción handoff "sin M2M/Eureka" vs skill "token propio + Gateway") | 04-T2, 05-T1, 15-T2 (RLS no, pero sí el cliente) | C1 el Día 1; tests con WireMock; stub con flag. Si no hay respuesta el Día 5, la demo usa el stub |
| R2 | **T01** | Permiso de lectura para PROFESSOR, estado de 2FA, `GET /api/users/audit`, secreto del Gateway, token de servicio | #3331, #1657, 06B-T3/T4, pantalla 11 | Tareas aisladas; si no hay respuesta el Día 6, pasan a "Necesita información" → S3 |
| R3 | **T11** | Topics de auditoría y notificaciones, `eventType` de `StudentAtHighRisk` | #312, 06B-T4 | El topic va en configuración; el outbox guarda igual |
| R4 | T02 | Pertenencia docente y fuente del CSAT | #310, #319 | Puerto con flag; el CSAT muestra "muestra insuficiente" |
| R5 | T05 | Topic de entregas (G2) | 08-T1 | Flag apagado y documentado |
| R6 | **T10** | Topic `sandbox.events` y formato del read model | T10-1 | Flag apagado; si no responde, T10-1 se corta |
| R7 | **T07** | Topic `llm.budget.events` y payload de `LLMBudgetAlert` | B-AL | Flag apagado; se corta si no llega |
| R8 | **T08** | Replay REST `/api/bank/**` y evento de saldo | T08-1 | Verificación aislada; se corta si no confirma |
| I1 | Interno | Carga al 88 %, con un integrante menos | Todo el sprint | **Corte primero:** flags (T10-1, B-AL, T08-1) → monitor (10-M1) → HU13 (#322) |
| I2 | Interno | Colisión de versiones de Flyway | Todas las migraciones | Versiones reservadas V18/V19/V20 (§10) |
| I3 | Interno | Archivos protegidos del frontend (`angular.json`, `package*.json`, `tsconfig*`) | Todas las PRs del frontend | No tocarlos |

---

## 12 · DoD (igual para los 9)

- **Nivel 0 · Tarea:** `mvn -B verify` en verde (tests, Checkstyle, **PMD 3.26**, **JaCoCo ≥ 0,90**); en el frontend, `npm run verify` (lint:all y Vitest) **sin `ng build` local**.
- **Nivel 1 · Historia:** cumple los CA; integración con Testcontainers donde haya eventos o BD; autorización 200/403 con roles del Gateway v3 (`ADMIN`, `GESTOR`, `PROFESSOR`, `STUDENT`, `MS`); **RLS verificado** en HU11/12/13 y US-15; OpenAPI actualizado; PR revisada por otra persona; sin secretos; docs y sitio sincronizados (D3).
- **Nivel 2 · Sprint:** suite completa en verde, Taiga al día, demo y retro.
- **Skills del Skill Hub:** `project-quality-gate` · `project-gitflow-guard` · `micro-to-micro-calls-with-a-service-token` (cliente T07) · `typed-configuration-env-vars-and-secret-files` (umbrales y flags) · `frontend-ui-kit-compliance` · `frontend-through-the-api-gateway`.

---

## 13 · Sprint 3 (backlog confirmado)

| Historia | Horas | Nota |
|---|---:|---|
| HU06 · Calibración sobre T07 (fachada) | 28 | gated por C1 |
| HU07 · PAR-14, veredicto y deriva sobre T07 | 16 | gated por C1 |
| HU14 · Umbrales y alertas | 22 | dependía de HU13 |
| US-15 fase 2 · Builder FE + specs | ~27 | cierra el requisito del profe |
| HU09 · Exportación | Could | — |