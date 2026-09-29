# Tareas Sprint 2 — Backlog dividido (9 integrantes)

> **Propuesta de Sprint Planning para revisar con el grupo.** Todavía **no está cargada en Taiga**: la carga (sprint, historias HT05–HT07 y tareas) se hace una vez que el grupo la apruebe. El sprint `G06 - Sprint 2` lo tiene que crear alguien con permiso sobre el proyecto (la cátedra).
> **Identificación de tareas:** las que tienen `#` ya existen en Taiga; las que tienen un ID como `04-T1` o `C1` son nuevas.
> **Sprint:** 28/09 al 11/10/2026 · 10 días hábiles · **Repos:** `2026-P4-BE/tpi-backoffice` y `2026-P4-FE/2026-PIV-TPI-FE`.
> **Integrantes:** 9. **Julieta Disca ya no forma parte del grupo**; su capacidad sale de la tabla y lo que tenía asignado se repartió.
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` desde `develop` → PR a `develop` con ≥ 1 aprobación. En el backend los commits van en español (`AGENTS.md`); en el frontend, en inglés.
> **Estimación:** historia = SP (Fibonacci) · tarea = horas.

## Decisiones del planning

1. **Capacidad:** se mantiene la tabla del Sprint 1, sin Julieta. **Pedido del PO:** más carga para **Máximo, Regina, Damián y Bruno**.
2. **Opción A (EP-03):** el Backoffice es la **fachada del ADMIN sobre `/admin/*` de T07**. No tiene tablas LLM ni calcula MAE. T07 decide `PASSED`/`FAILED`; **el Backoffice es dueño del valor de PAR-14**.
   - Las tareas del golden set (#3537–#3540 y #286) se redefinen.
   - Las cerradas #281, #282, #283 y #285 quedan "reemplazadas por la opción A". El código está en `feature/mvp-s6-golden-set-runs`, PR #30 cerrada.
3. **Historias Done con tareas reabiertas:** #17, #28 y #182 **quedan Done**. Sus tareas pendientes (#3331, #291 y #3512), más #1657 de HT04, pasan a la historia técnica nueva **HT05**.
4. **Regla de riesgo (HU11):** vale la de `uh/US-11.md`:
   - **ROJO:** más de 10 días de inactividad o reprobación mayor a 60 %;
   - **AMARILLO:** entre 5 y 10 días, o reprobación entre 40 y 60 %;
   - **VERDE:** aprobación de 70 % o más.

   Los umbrales van en configuración tipada. "Vidas agotadas" queda detrás de un flag hasta que haya datos de T08. Las dos versiones que existían cumplen los tres BDD; se elige la de la historia porque es la que tiene CA.
5. **HU09 #20** figura en el sprint "G01 - Sprint 1" por error. Hay que pasarla al backlog (Could). **US-15** no entra en este sprint (Won't): no está en Taiga y no tiene definición lista.
6. **Salida de Julieta:** se pierden 51,5 h de capacidad.
   - **HU14 #34 sale del sprint** y pasa al Sprint 3 (sigue siendo Should). Con ella adentro, la carga superaba el 80 %.
   - Las 25 h restantes de Julieta se repartieron entre Luciano, Mateo, Joaquín y Valentina, respetando que nadie teste ni revise su propio código.

---

## 1 · Capacidad

`Teóricas = h/día × (días − ausencias)` → `Efectivas = teóricas − 8,5 h de ceremonias` → `Ajustada = efectivas × % de dedicación`

| Dev | Integrante | Rol | h/día | Días | Aus. | Teóricas | Efectivas | % | **Ajustada** | Plan | % uso |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Paz, Luciano | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 95 % | **39,4** | 30 | 76 % |
| 2 | Carballo Juarez, Mateo | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 100 % | **41,5** | 32 | 77 % |
| 3 | Baigorria, Damián ★ | PIV | 5 | 10 | 2 | 40 | 31,5 | 100 % | **31,5** | 27 | 86 % |
| 4 | Cortez, Joaquín | PIV | 5 | 10 | 2 | 40 | 31,5 | 88 % | **27,7** | 20 | 72 % |
| 6 | Maldonado, Valentina | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 85 % | **35,3** | 27 | 76 % |
| 7 | Cerquatti, Máximo ★ | MSII+PIV | 6 | 10 | 0 | 60 | 51,5 | 90 % | **46,4** | 38 | 82 % |
| 8 | Cerasulo, Regina ★ | MSII+PIV | 5 | 10 | 0 | 50 | 41,5 | 85 % | **35,3** | 30 | 85 % |
| 9 | Gianoli, Bruno ★ | PIV | 4 | 10 | 0 | 40 | 31,5 | 90 % | **28,4** | 24 | 85 % |
| 10 | Ducart, Ana Paula | MSII | 5 | 10 | 2 | 40 | 31,5 | 80 % | **25,2** | 19 | 75 % |
| | **Total** | | | | **6** | **420** | **343,5** | | **310,6** | **247** | **79,5 %** |

- Se mantiene la numeración del Sprint 1; el Dev 5 era Julieta.
- **★ Foco del PO:** Máximo, Regina, Damián y Bruno tienen el 82–86 % de su capacidad ocupada; el resto, entre el 72 y el 77 %.
- **79,5 % es el tope del rango sano (60–80 %).** Hay poco margen: si aparece algo imprevisto, lo primero que se corta es HU13.

---

## 2 · Alcance (MoSCoW)

| Prioridad | Historia | Taiga | SP | Horas |
|---|---|---|---:|---:|
| Must | HU04 · Proveedores y modelos (fachada T07) | #18 | 5 | 24 |
| Must | HU05 · Conmutación de modelos (fachada T07) | #26 | 5 | 21 |
| Must | HU06 · Calibración institucional (golden set) sobre T07 | #27 | 5 | 28 |
| Must | HU07 · PAR-14, veredicto y deriva sobre T07 | #29 | 5 | 16 |
| Must | HU08 · Ingesta: consumidores de T07 y T05 con flags | #1628 | 5 | 11 |
| Must | HT01 · Contratos entre temas | #1629 | 3 | 9 |
| Must | **HT05** · Frontend: acceso por rol, rutas `/backoffice` y deuda del Sprint 1 | nueva | 5 | 22 |
| Must | **HT06** · Backend: orden del outbox por `param_key` y confianza en el Gateway | nueva | 3 | 18 |
| Must | **HT07** · Documentación y proceso del Sprint 2 | nueva | 3 | 8 |
| Should | HU11 · Read model y riesgo por cohorte | #25 | 5 | 27 |
| Should | HU12 · Panel docente con RLS | #24 | 5 | 37 |
| Should | HU13 · Indicadores con anonimato | #111 | 5 | 26 |
| **Total** | | | **54** | **247** |
| Should → Sprint 3 | HU14 · Umbrales y alertas | #34 | 3 | fuera |
| Could | HU09 · Exportación | #20 | 5 | fuera |
| Won't | US-15 · Reportes dinámicos | — | 8 | fuera |

**Por qué este corte:**
- Las Must dejan la gobernanza de IA real sobre T07 y todas las pantallas detrás del Gateway.
- HU11 → HU12 → HU13 es la siguiente cadena de valor y solo usa datos que ya ingerimos (T03 y T02).
- HU14 depende de HU13 y es la primera que sale sin la capacidad de Julieta.
- **Velocidad:** los 54 SP no se comparan con los 19 del Sprint 1, porque HU04, HU05, HU08 y HT01 ya tienen la mayor parte de sus tareas cerradas. La medida real son las horas.

---

## 3 · EP-03 · Modelos LLM y calibración (fachada sobre T07)

**Referencias:**
- Skill Hub: `backoffice-admin-and-llm-service-integration-contract` v2 (dueño T07), `llm-service-http-contract` y `micro-to-micro-calls-with-a-service-token`. De esta última salen las reglas del cliente: timeout de 3 s, reintento solo en GET, propagar `X-Request-Id`, y nunca traducir un 401/403 de T07 a 500.
- Se reutilizan las interfaces congeladas `LlmAdminClient` y `LlmProviderClient`, con sus stubs `DefaultLlmAdminClient` y `DefaultLlmProviderClient`, en `administration/services/llm/**`.
- **Antes de 04-T1 se necesita la respuesta a C1.**

### HU04 #18 — 24 h
> Ramas: `feature/tema-12-llm-admin-client` (04-T1, merge el Día 3) y `feature/tema-12-llm-providers-models-real` (merge el Día 5).

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| 04-T1 | Infraestructura del cliente HTTP hacia T07: `RestClient` administrado, autenticación de servicio según C1, `problem+json` → `LlmProviderException`/`LlmModelException`, URL base en configuración tipada; el stub queda como fallback con flag | BACKEND | Máximo | 6 |
| 04-T2 | Cliente real de proveedores y credenciales (providers, provider-credentials, discover-models, test-model). La key viaja a T07; nunca se loguea ni se devuelve | BACKEND | Regina | 5 |
| 04-T3 | Conectar la pantalla 09 al backend (quitar los datos en memoria y pasar a `/api/backoffice/llm/...`) | FRONTEND | Regina | 4 |
| 04-T4 | Tests de integración con WireMock del cliente de proveedores: éxito, 404, 409, 503 y key enmascarada | TEST | Luciano | 5 |
| 04-T5 | Peer review de seguridad de credenciales y del cliente T07 | REVISION | Bruno | 2 |
| 04-T6 | OpenAPI de la fachada de proveedores y modelos | DOCUMENTACION | Regina | 2 |

### HU05 #26 — 21 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| 05-T1 | Cliente real de modelos evaluadores (listar, activo, desplegar, activar, borrar) sobre 04-T1 | BACKEND | Mateo | 5 |
| #276 | Modal de conmutación con advertencia (modelo actual → nuevo, confirmación explícita, textos de UI en español) | FRONTEND | Mateo | 3 |
| 05-T3 | Conectar la pantalla 10 al backend (quitar los datos en memoria) | FRONTEND | Mateo | 3 |
| 05-T4 | Tests con WireMock de la activación: 200, 409 (no aprobado) y 503 (T07 caído) | TEST | Luciano | 4 |
| 05-T5 | Contrato: `MODEL_CHANGED` lo publica T07 y el Backoffice deja de emitir `ModelProviderChanged` | DOCUMENTACION | Ana | 2 |
| 05-T6 | Peer review de la fachada de modelos (mapeo de errores, unicidad delegada en T07) | REVISION | Joaquín | 1 |
| 05-T7 | Specs de las pantallas 09 y 10 conectadas | TEST | Valentina | 3 |

### HU06 #27 — 28 h (calibración institucional sobre T07)
> Rama: `feature/tema-12-calibration-facade` · merge el Día 6.

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| #3537 | **Redefinida:** fachada del perfil de calibración institucional (golden set y rúbrica) sobre T07 | BACKEND | Joaquín | 4 |
| #3539 | **Redefinida:** fachada de corridas de calibración (crear, listar, detalle; `maeFinal`, `maxIndividualError` y veredicto de T07) | BACKEND | Bruno | 5 |
| #3538 | **Redefinida:** pantalla del perfil de calibración (parte 12) | FRONTEND | Joaquín | 5 |
| #3540 | **Redefinida:** pantalla de corridas de calibración (parte 13) | FRONTEND | Bruno | 5 |
| 06-T5 | Tests de integración con WireMock de la fachada de calibración | TEST | Máximo | 4 |
| 06-T6 | Diagrama de secuencia ADMIN → Backoffice → T07 (calibración) | DOCUMENTACION | Ana | 3 |
| 06-T7 | Peer review de la fachada de calibración | REVISION | Regina | 1 |
| 06-T8 | OpenAPI de la fachada de calibración | DOCUMENTACION | Joaquín | 1 |

### HU07 #29 — 16 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| 07-T1 | PAR-14 como fuente de la tolerancia: validar rango y formato en el registro, y comprobar que T07 lo lea con `backoffice.parameters.read` | BACKEND | Damián | 3 |
| 07-T2 | Endpoint del estado de calibración del modelo activo (último veredicto y marca de deriva, leídos de T07) | BACKEND | Bruno | 4 |
| #284 | Indicador de veredicto y deriva, y banner de conmutación automática | FRONTEND | Joaquín | 3 |
| 07-T4 | Tests del estado de calibración y de la validación de PAR-14 | TEST | Máximo | 3 |
| #286 | **Redefinida:** T07 calcula el MAE y el veredicto; el Backoffice gobierna PAR-14; la deriva la emite T07 | DOCUMENTACION | Bruno | 2 |
| 07-T6 | Peer review | REVISION | Mateo | 1 |

---

## 4 · EP-04 · Contratos e ingesta

### HU08 #1628 — 11 h
> Rama: `feature/tema-12-hu08-consumers` · merge el Día 7. Los flags quedan apagados si el contrato no se confirma.

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| 08-T1 | Consumidores de `llm.events` (T07; filtrar por `eventType` según `llm-service-kafka-contract`) y de T05, detrás de flags, con deduplicación y DLT | BACKEND | Valentina | 5 |
| 08-T3 | Tests de integración: nuevo, duplicado, malformado → DLT, flag apagado | TEST | Bruno | 3 |
| 08-T4 | Actualizar el mapeo de contratos de lectura con T07 y T05 | DOCUMENTACION | Valentina | 2 |
| 08-T5 | Peer review de los consumidores | REVISION | Luciano | 1 |

### HT01 #1629 — 9 h · **se mandan el Día 1**

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| C1 | Confirmar con T07 y T01 la llamada a `/api/llm/admin/*`: ruta, Gateway o Eureka, token `client_credentials` o headers, y scopes | DOCUMENTACION | Máximo | 2 |
| C2 | Confirmar con T11 los topics de auditoría (`identity.audit` o `identity.audit.events`) y de notificaciones, y el `eventType` de `StudentAtHighRisk` | DOCUMENTACION | Mateo | 2 |
| C3 | Pedir a T02 la API de pertenencia docente por cohorte (HU12) y la fuente del CSAT (HU13) | DOCUMENTACION | Damián | 2 |
| C4 | Corregir el §6 del contrato de T07 (fachada) y registrar los acuerdos en `CONTRATOS.md` | DOCUMENTACION | Ana | 3 |

---

## 5 · Historias técnicas

### HT05 (nueva) · Frontend: acceso por rol, rutas `/backoffice` y deuda del Sprint 1 — 5 SP · 22 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
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
|---|---|---|---|---:|
| 06B-T1 | Orden estricto por `param_key` en `OutboxMessageRepository.findReadyToPublish`: no tomar una fila si hay una `PENDING` más vieja de la misma key (US-02 CA4; detalle en la PR #46) | BACKEND | Luciano | 4 |
| 06B-T2 | Test de integración del orden: falla v1 y v2 no sale antes (Testcontainers con Kafka) | TEST | Máximo | 4 |
| 06B-T3 | Verificar `GATEWAY_SHARED_SECRET` (`GatewayTrustProperties`) con el mecanismo que acuerde T01, sin romper el entorno local | BACKEND | Máximo | 3 |
| 06B-T4 | Configurar el topic de auditoría confirmado (C2) y la auditoría delegada en T01 (pantalla 11 sin 502) | BACKEND | Máximo | 2 |
| 06B-T5 | Peer review de concurrencia del outbox y del secreto del Gateway | REVISION | Valentina | 2 |
| 06B-T6 | Documentar el orden por key en el contrato del consumidor | DOCUMENTACION | Luciano | 1 |
| 06B-T7 | Peer review de las PRs abiertas del backend #47, #48 y #50 (de Mateo) | REVISION | Luciano | 2 |

### HT07 (nueva) · Documentación y proceso del Sprint 2 — 3 SP · 8 h

| ID | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| D1 | Diagrama de secuencia del cambio de parámetro (quedó diferido del Sprint 1) | DOCUMENTACION | Ana | 3 |
| D3 | Sincronizar `docs/backend/docs` y el sitio con lo real: fachada T07, auditoría vía T01, `/backoffice`, roles del Gateway v3 | DOCUMENTACION | Ana | 3 |
| D4 | Carga y sincronización de Taiga del Sprint 2 y acta de la retro del Sprint 1 | DOCUMENTACION | Ana | 2 |

---

## 6 · EP-05 · Analítica

### HU11 #25 — 27 h
> Ramas: `feature/tema-12-hu11-read-model` (solo V18, merge el Día 3) y `feature/tema-12-hu11-risk` (merge el Día 6).

| # | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| #303 | Migración `V18__reporting_cohort_student_summary` y read model con índices | BACKEND | Damián | 6 |
| #304 | Algoritmo de riesgo con la regla de US-11.md; umbrales en configuración tipada; "vidas agotadas" detrás de flag | BACKEND | Damián | 6 |
| #305 | Job programado de recálculo desde `ingested_event` (T03 `challenge.events` y T02 `course.events`) | BACKEND | Valentina | 5 |
| #306 | Partición de equivalencia y valores límite: 10/11 días, 4/5 días, 40/60 %, 70 % | TEST | Regina | 5 |
| #307 | Reglas de riesgo y esquema del read model | DOCUMENTACION | Mateo | 3 |
| #308 | Peer review del modelado analítico | REVISION | Máximo | 2 |

### HU12 #24 — 37 h · DoD con **RLS verificado**
> Rama: `feature/tema-12-hu12-teacher-panel` · merge el Día 8.

| # | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
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
> Rama: `feature/tema-12-hu13-indicators` · merge el Día 8. **Si el sprint se atrasa, es la primera que se corta.**

| # | Tarea | Tipo | Dev | h |
|---|---|---|---|---:|
| #319 | Agregación de indicadores: aprobación, abandono y actividad semanal; CSAT solo si hay datos | BACKEND | Mateo | 6 |
| #320 | Anonimato por umbral mínimo, en configuración (verificar en `PARAMETROS.md` si corresponde a un PAR: hay conflicto con PAR-18) | BACKEND | Regina | 4 |
| #321 | `GET ${app.api.private-path}/reports/platform`, solo ADMIN, sin ranking | BACKEND | Mateo | 4 |
| #322 | Dashboard de KPIs con aviso de "muestra insuficiente" | FRONTEND | Valentina | 5 |
| #323 | Tests de anonimato (4, 5 y 6 respuestas) y 403 para quien no es ADMIN | TEST | Máximo | 4 |
| #324 | Políticas de privacidad y fórmulas | DOCUMENTACION | Joaquín | 2 |
| #325 | Peer review de privacidad | REVISION | Damián | 1 |

---

## 7 · Matriz de revisión cruzada (nadie testea ni revisa lo suyo)

| Código de… | Lo testea | Lo revisa |
|---|---|---|
| 04-T1, cliente T07 (Máximo) | Luciano (04-T4 y 05-T4) | Bruno (04-T5) |
| 04-T2 y 04-T3, proveedores (Regina) | Luciano (04-T4) · Valentina (05-T7) | Bruno (04-T5) |
| 05-T1, #276 y 05-T3, modelos (Mateo) | Luciano (05-T4) · Valentina (05-T7) | Joaquín (05-T6) |
| #3537/#3538 (Joaquín) · #3539/#3540 (Bruno) | Máximo (06-T5) | Regina (06-T7) |
| 07-T1 (Damián) · 07-T2 (Bruno) · #284 (Joaquín) | Máximo (07-T4) | Mateo (07-T6) |
| 08-T1, consumidores (Valentina) | Bruno (08-T3) | Luciano (08-T5) |
| #3512 (Máximo) · #3331 (Bruno) · #291 y 05-N2 (Valentina) · #1657 (Regina) · 05-N1 (Luciano) | Mateo (05-N3) | Joaquín (05-N4) |
| 06B-T1, orden del outbox (Luciano) · 06B-T3/T4 (Máximo) | Máximo (06B-T2, sobre el código de Luciano) | Valentina (06B-T5) |
| PRs #47, #48 y #50 (Mateo) | — | Luciano (06B-T7) |
| #303/#304 (Damián) · #305 (Valentina) | Regina (#306) | Máximo (#308) |
| #310 (Regina) · #311 (Máximo) · #312 (Luciano) · #313 (Damián) | Luciano (#314, sobre #310 y #311) · Damián (#315, sobre #311 y #312) · Joaquín (12-T9, sobre #313) | Mateo (#317) |
| #319 y #321 (Mateo) · #320 (Regina) · #322 (Valentina) | Máximo (#323) | Damián (#325) |

> 06B-T3 y 06B-T4 son de Máximo y los revisa Valentina (06B-T5); sus tests van dentro de la misma PR y los valida quien revisa.

---

## 8 · Resumen de carga (los 8 que codifican cubren las 5 capas)

| Dev | Integrante | Capacidad | Horas | % uso | BACK | FRONT | TEST | REV | DOC |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | Luciano Paz | 39,4 | 30 | 76 % | 8 | 4 | 14 | 3 | 1 |
| 2 | Mateo Carballo Juarez | 41,5 | 32 | 77 % | 15 | 6 | 3 | 3 | 5 |
| 3 | Damián Baigorria ★ | 31,5 | 27 | 86 % | 15 | 5 | 4 | 1 | 2 |
| 4 | Joaquín Cortez | 27,7 | 20 | 72 % | 4 | 8 | 2 | 3 | 3 |
| 6 | Valentina Maldonado | 35,3 | 27 | 76 % | 10 | 10 | 3 | 2 | 2 |
| 7 | Máximo Cerquatti ★ | 46,4 | 38 | 82 % | 17 | 2 | 15 | 2 | 2 |
| 8 | Regina Cerasulo ★ | 35,3 | 30 | 85 % | 15 | 7 | 5 | 1 | 2 |
| 9 | Bruno Gianoli ★ | 28,4 | 24 | 85 % | 9 | 8 | 3 | 2 | 2 |
| 10 | Ana Paula Ducart | 25,2 | 19 | 75 % | — | — | — | — | 19 |
| | **Total** | **310,6** | **247** | **79,5 %** | | | | | |

### Cómo se repartió lo que tenía Julieta

| Tarea | h | Pasa a |
|---|---:|---|
| 04-T4 · Tests con WireMock del cliente de proveedores | 5 | Luciano |
| #312 · `StudentAtHighRisk` por outbox | 4 | Luciano |
| 06B-T7 · Peer review de las PRs abiertas #47, #48 y #50 | 2 | Luciano |
| #319 · Agregación de indicadores | 6 | Mateo |
| #307 · Reglas de riesgo y esquema del read model | 3 | Mateo |
| 05-N2 · Dashboard: ocultar accesos no permitidos | 2 | Valentina |
| 05-T6 · Peer review de la fachada de modelos | 1 | Joaquín |
| #324 · Políticas de privacidad y fórmulas | 2 | Joaquín |
| #326 y #330 (umbrales) | 6 | Salen del sprint con HU14 #34 |

---

## 9 · Secuencia de merges

**Día 1:** se mandan C1, C2 y C3. Las interfaces LLM ya están congeladas, así que Regina, Mateo, Bruno y Joaquín arrancan con mocks.
**Versiones de Flyway reservadas:** V18 (HU11) y V19 (HU12, RLS). Nadie usa otro número sin avisar.

| Día | Backend (PR a `develop`) | Frontend |
|---|---|---|
| 2 | — | #3512 guards |
| 3 | `fix/tema-12-outbox-key-ordering` · `feature/tema-12-llm-admin-client` · `feature/tema-12-hu11-read-model` (V18) | 05-N1, migración a `/backoffice` |
| 5 | `feature/tema-12-llm-providers-models-real` | integración `feature/tema-12-backoffice` → `develop` (Luciano) |
| 6 | `feature/tema-12-calibration-facade` · `feature/tema-12-hu11-risk` | pantallas 09 y 10 conectadas, #276 |
| 7 | `feature/tema-12-hu08-consumers` · `fix/tema-12-gateway-trust` (si T01 respondió) | partes 12 y 13, #284, #291, 05-N2 |
| 8 | `feature/tema-12-hu12-teacher-panel` (V19) · `feature/tema-12-hu13-indicators` | #3331 y #1657 (si T01 respondió) |
| 9–10 | ajustes y cierre | #313, #322 · integración final |

---

## 10 · Riesgos y dependencias externas

| # | Equipo | Dependencia | Bloquea | Mitigación |
|---|---|---|---|---|
| R1 | **T07** | Ruta, autenticación y scopes de `/api/llm/admin/*`. **Hay una contradicción que resolver:** el handoff dice "sin M2M, directo por Eureka"; la skill `micro-to-micro-calls-with-a-service-token` dice "token propio y Gateway" | 04-T2, 05-T1, #3537, #3539, 07-T2 | C1 el Día 1; tests con WireMock; stub con flag. Si no hay respuesta el Día 5, la demo usa el stub |
| R2 | **T01** | Permiso de lectura para PROFESSOR en `core/`, estado de 2FA, `GET /api/users/audit`, mecanismo del secreto del Gateway, token de servicio | #3331, #1657, 06B-T3/T4, pantalla 11 | Tareas aisladas; si no hay respuesta el Día 6, pasan a "Necesita información" y al Sprint 3 |
| R3 | **T11** | Topics de auditoría y notificaciones, y los `eventType` | #312, 06B-T4 | El topic va en configuración; el outbox guarda igual |
| R4 | T02 | Pertenencia docente y fuente del CSAT | #310, #319 | Puerto con flag; el CSAT muestra "muestra insuficiente" |
| R5 | T05 | Topic de entregas (G2) | 08-T1 | Flag apagado y documentado |
| I1 | Interno | Carga al 79,5 %, con un integrante menos | Todo el sprint | Si hay atraso, se corta HU13 primero |
| I2 | Interno | Colisión de versiones de Flyway | Todas las migraciones | Versiones reservadas (§9) |
| I3 | Interno | Archivos protegidos del frontend (`angular.json`, `package*.json`, `tsconfig*`) | Todas las PRs del frontend | No tocarlos |

---

## 11 · DoD (igual para los 9)

- **Nivel 0 · Tarea:**
  - `mvn -B verify` en verde: tests, Checkstyle, **PMD 3.26** y **JaCoCo ≥ 0,90** (es el gate de `verify.yml`);
  - en el frontend, `npm run verify` (lint:all y Vitest) **sin `ng build` local**.
- **Nivel 1 · Historia:**
  - cumple los CA;
  - integración con Testcontainers donde haya eventos o BD;
  - autorización 200/403 con los roles del Gateway v3 (`ADMIN`, `GESTOR`, `PROFESSOR`, `STUDENT`, `MS`);
  - **RLS verificado** en HU11, HU12 y HU13;
  - OpenAPI actualizado;
  - PR revisada por otra persona;
  - sin secretos;
  - docs y sitio sincronizados (D3).
- **Nivel 2 · Sprint:** suite completa en verde, Taiga al día (cada uno mueve su tarjeta), demo y retro.
- **Skills del Skill Hub a aplicar:**
  - `project-quality-gate` y `project-gitflow-guard`;
  - `micro-to-micro-calls-with-a-service-token` (cliente T07);
  - `typed-configuration-env-vars-and-secret-files` (umbrales y flags);
  - `frontend-ui-kit-compliance` y `frontend-through-the-api-gateway` (pantallas).
