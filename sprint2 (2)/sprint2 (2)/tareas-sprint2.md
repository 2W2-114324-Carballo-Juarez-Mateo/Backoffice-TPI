# Tareas Sprint 2 — Backoffice (Tema 12) · PLAN CORREGIDO

> **Sprint:** 28/09 → 11/10/2026 · **Equipo:** TPI-G06 (9 integrantes) · **Repos:** `2026-P4-BE/tpi-backoffice` · `2026-P4-FE/2026-PIV-TPI-FE`
> **Base:** propuesta unificada del grupo (`sprint2/tareas-sprint2.md`, se conserva sin tocar) + auditoría del Sprint 1 (`auditoria-sprint1.md`) + retrospectiva (`retrospectiva-sprint1.md`). Cada cambio contra la propuesta está justificado en `correcciones-propuesta.md`.
> **Estimación:** **sin horas** (decisión del equipo: con IA la estimación en horas engaña). Historia = **SP Fibonacci** · tarea = **tamaño relativo** (S/M/L/XL). La capacidad se expresa como **disponibilidad** (alta/media/baja), tomada de la tabla de capacidad que ya había armado el grupo.
> **Fuente única de verdad:** este archivo. Los `dev-XX.md` se derivan de acá. Si algo no coincide, manda este archivo.

---

## 0 · Cómo leer este plan

| Si sos… | Leé primero |
|---|---|
| Dev que programa | Tu `dev-XX.md` → §5 (contratos compartidos) → §8 (secuencia) → `revision-pr.md` (lo que te van a revisar) |
| Revisor/a de una PR | `revision-pr.md` → §9 (matriz: qué revisás y de quién) |
| Ana (MSII) | `dev-09.md` → §6.9 (wiki de G06) → §3 (seguimiento de contratos en Taiga) |
| Quien coordina | §1 (decisiones) → §3 (contratos) → §8 (checkpoints) → §11 (corte) |

**Tamaños (relativos, no son tiempo):**

| Tamaño | Significa | Ejemplo |
|---|---|---|
| **S** | Cambio acotado: 1 clase/componente principal + su test | Validación de PAR-14, abrir la PR de una rama ya terminada |
| **M** | Una capa completa de un caso de uso | Cliente real de modelos T07, pantalla conectada |
| **L** | Caso de uso vertical: varias clases + migración o pantalla | RLS + puerto de pertenencia, proyector de read models |
| **XL** | Motor o pieza transversal con invariantes de seguridad | Motor de reportes dinámicos + `run` |

---

## 1 · Decisiones del plan

| # | Decisión | Por qué (evidencia) |
|---|---|---|
| D-01 | **Primero se cierra el Sprint 1.** Lo que quedó en ramas sin PR, tareas reabiertas y contratos sin firmar entra como historias de arrastre (§4), antes que lo nuevo. | 11 tareas abiertas del Sprint 1 en Taiga; 6 ramas BE y 3 FE con trabajo fuera de `develop` (ver `auditoria-sprint1.md`). |
| D-02 | **Un PR de contratos compartidos del Sprint 2 (S2-00) al inicio**, congelado, como hizo el Sprint 1 con S3. Todos codifican contra esas interfaces con mocks. | Fue lo que mejor funcionó en el S1. La propuesta no lo tenía: HU12 tenía **7** devs y US-15 **9** devs sobre los mismos archivos. |
| D-03 | **Un dueño por paquete/clase.** Si una historia tiene varias tareas de backend en el mismo servicio, se consolidan en una persona (HU13 → Mateo, HU14 → Regina, motor + `run` de US-15 → Bruno, evento de riesgo → quien calcula el riesgo). | Retro: "tareas que se pisaban". En la propuesta #319/#320/#321 (HU13) eran 3 devs en el mismo servicio; 15-T2 y 15-T4 dos devs en el mismo motor. |
| D-04 | **El Backoffice no produce datos: los lee** (PDF arquitectura, pág. 14). Ningún reporte se programa contra un contrato en `SOLICITUD LISTA` sin **puerto + flag + fallback**. Los contratos de lectura son la ruta crítica (§3). | T02 no respondió la solicitud y T05 ni siquiera tiene solicitud; sin T02 no hay pertenencia docente, ni padrón, ni CSAT. |
| D-05 | **Frescura ≤ 15 min (PAR-23) en cada respuesta de reporte**, calculada al leer. El recálculo de read models corre cada ≤ 5 min. | En `develop` la frescura ya se calcula al leer (`IngestionCounter`: "not stored: computed at read time"). El 10-M1 de la propuesta (un `@Scheduled` que marca `isStale`) duplicaba y contradecía ese diseño. |
| D-06 | **Sin comparación entre docentes** es un invariante verificable: ningún endpoint acepta ni devuelve la dimensión docente; el PROFESOR recibe 403 ante un curso ajeno; la vista de plataforma del ADMIN no ordena ni rankea por métrica. | HU12 CA3/CA4 (Taiga), RF-ENC-08 (PRD). |
| D-07 | **Anonimato de encuestas según el PRD:** el PROFESOR ve puntajes solo si `respuestas ≥ PAR-18` **y** el curso cerró (RF-ENC-13). KPI-01/02 = % de 4–5 y % de 1–2 sobre respuestas emitidas; las abstenciones se informan aparte y no entran al denominador (RF-ENC-10). | La propuesta pedía "umbral mínimo" y "CSAT promedio"; el PRD exige distribución, abstenciones y el corte al cierre. **PAR-18 ya es ese umbral** (no hay conflicto con PAR-18: es el mismo parámetro). |
| D-08 | **Solo se emiten eventos del catálogo de T11:** `STUDENT_AT_HIGH_RISK` (HU12) y `EXPORT_READY` (HU09) a `notifications.events` por outbox. **`THRESHOLD_BREACHED` no se emite** (HU14 usa alertas internas) salvo que T11 lo registre. | `CONTRATOS_MAPEO_TOPICS.md` (v3): "No se emiten `DATA_STALE_DETECTED` ni `THRESHOLD_BREACHED`". |
| D-09 | **HU06/HU07 (calibración) siguen como fachada de T07 con gate C1.** Si T07 no confirma `/api/llm/admin/*` en el **CP2 (02/10)**, quedan con stub + flag y sus dueños refuerzan US-15/HU09. La rama `feature/mvp-s6-golden-set-runs` **no se mergea** (contradice la Opción A). | PR #30 cerrada; el golden set y el MAE son de T07. |
| D-10 | **Carga realista:** núcleo (Must/Should) + tareas con gate + *stretch* (Could). El stretch **solo arranca** cuando el dueño tiene su núcleo mergeado. | Retro: "planificar sin ajustar la capacidad". La propuesta cargaba 117 % nominal y a Bruno 148 % con la ruta crítica (15-T2). |
| D-11 | **Regla de riesgo (HU11):** la de Taiga, evaluada en orden RED → YELLOW → GREEN, con **R-1: el hueco va a YELLOW** y **R-2: las tasas solo se calculan con ≥ 3 intentos** (decididas por el grupo el 29/09, §6.1). Umbrales en configuración tipada (`reporting.risk.*`), no en PAR. | La regla de Taiga dejaba sin clasificar a un alumno con 60–70 % de aprobación y < 5 días de inactividad, y con 1 intento reprobado lo mandaba a RED. |
| D-12 | **Flyway reservado V18–V26 con dueño** (§7). Nadie usa otro número sin avisar. | En el S1 la reserva funcionó; la propuesta reservaba 3 versiones para 6 migraciones. |
| D-13 | **Ana (MSII) trabaja solo en Taiga (tablero y wiki) y Draw.io.** Todo lo que vive en el repo (contratos `.md`, OpenAPI, `docs/`) lo escribe el dev dueño en su PR. Su entregable principal es la **wiki de G06**, que hoy no existe. | Ana no tiene commits en los repos; la cátedra exige una página de wiki por grupo y tema (`guia-doc-proyecto-por-grupo`) y G06 es de los pocos grupos sin página. |

> **Decisiones tomadas por el grupo (29/09):** **R-1** el hueco de la regla de riesgo se clasifica **YELLOW** · **R-2** mínimo de **3 intentos** para calcular las tasas · **P-12** **PAR-12 es del Backoffice** (se siembra en V24 y se corrige `AGENTS.md`). Detalle en §6.1 y §4.2.

---

## 2 · Alcance (MoSCoW, SP)

| Prioridad | Historia | Taiga | SP | Estado de partida |
|---|---|---|---:|---|
| **Must (arrastre S1)** | HT08 · Cierre del Sprint 1: ramas, release v1.0.0 y Taiga coherente | nueva | 2 | Ramas sin PR, PR #54 abierta, estados incoherentes |
| **Must (arrastre S1)** | HU01-bis · Registro PAR-01..24 completo (PAR-12, PAR-14, IT de US-01) | #17 | 2 | PAR-12 sin sembrar, PAR-14 sin validar rango, IT sin PR |
| **Must (arrastre S1)** | HU04 · Proveedores LLM reales (fachada T07, solo ADMIN) | #18 | 5 | Endpoints ADMIN ok, cliente **stub** |
| **Must (arrastre S1)** | HU05 · Modelos y conmutación reales (fachada T07) | #26 | 5 | Endpoints ADMIN ok, cliente **stub**, pantalla 10 con mock |
| **Must (arrastre S1)** | HT01 · Contratos de lectura con los 6 temas (firmados) | #1629 | 5 | T02/T11 sin respuesta, T05 sin solicitud, tabla de firmas vacía |
| **Must (arrastre S1)** | HT05 · Frontend: acceso por rol, `/api/backoffice` y deuda S1 | nueva | 5 | Guards y badge terminados **en ramas sin PR** |
| **Must (arrastre S1)** | HT06 · Outbox: orden estricto por clave + borde del Gateway | nueva | 3 | Detectado en la PR #46 |
| **Must** | HU11 · Read models y cálculo de riesgo por cohorte | #25 | 5 | Nada en `develop` |
| **Must** | HU12 · Panel docente con RLS, sin comparación y alerta de riesgo | #24 | 5 | Nada en `develop` |
| **Must** | HU10-bis · Frescura ≤ 15 min en cada reporte | #28 | 3 | Frescura solo en ingesta |
| **Must** | US-15 fase 1 · Reportes docentes dinámicos (backend) | nueva | 5 | Requisito de la cátedra |
| **Must** | HT07 · Wiki de G06 en Taiga (plantilla de la cátedra), diagramas y demo | nueva | 3 | **No existe ninguna página de G06 en la wiki** |
| **Should** | US-15 fase 2 · Builder de reportes (frontend) | nueva | 3 | Depende de la fase 1 |
| **Should** | HU13 · KPIs CSAT 5 estrellas con anonimato | #111 | 5 | Depende de T02 (encuestas) |
| **Should (gate C1)** | HU06 · Calibración institucional (fachada T07) | #27 | 5 | #3537–#3540 abiertas |
| **Should (gate C1)** | HU07 · PAR-14, veredicto y deriva (fachada T07) | #29 | 5 | #284, #286 abiertas |
| **Should (gate contrato)** | HU08-bis · Consumidores T07/T05 con flag | #1628 | 3 | Consumidores T03/T02/T08/T10 ya en `develop` |
| **Could (stretch)** | HU14 · Alertas configurables (internas) | #34 | 3 | — |
| **Could (stretch)** | HU09 · Exportación asíncrona CSV con `EXPORT_READY` | #20 | 5 | UI de export (slice 07) ya existe |
| | **Total Must** | | **48** | (el S1 comprometió 34 SP y cerró 19) |
| | **Total con Should** | | **69** | |
| | **Total con Could** | | **77** | |

**Fuera del Sprint 2 (backlog, con motivo):**

| Ítem de la propuesta | Motivo | Vuelve cuando |
|---|---|---|
| B-AL · alerta de presupuesto LLM (`llm.budget.events`) | El topic **no está registrado en T11** y el contrato con T07 no está firmado. | T11 registra el topic y T07 firma el payload de `LLMBudgetAlert`. |
| T10-1 · proyección de progreso/XP de T10 | El consumidor `SandboxEventConsumer` **ya existe** (flag apagado); falta el contrato (EN CURSO). | T10 confirma el payload de `sandbox.events` (C6). |
| T08-1 · replay REST de T08 | `EconomyTransactionConsumer` ya existe (flag apagado); ningún reporte del S2 usa saldos. | Se agregue una métrica de economía al catálogo de US-15. |
| 06B-T7 · revisar PRs #47, #48 y #50 | **Ya se mergearon el 29/09.** | — (obsoleta) |

---

## 3 · Contratos de lectura — la ruta crítica (PDF arquitectura, pág. 14)

> La lámina 6 lo dice sin vueltas: el Tema 12 no tiene dominio propio y no puede mostrar nada hasta que seis equipos expongan sus lecturas; si no se acuerdan contratos temprano, el Backoffice queda bloqueado.
> **Regla D-04:** sin contrato firmado se programa contra un **puerto** con **flag** y un **fallback documentado**. Nunca contra un payload supuesto sin marcarlo.

### 3.1 · Matriz de fuentes → reportes

| Tema | Qué necesitamos exactamente | Lo usa | Mecanismo | Estado del contrato | En `develop` hoy | Resp. T12 | Si no llega al CP2 (02/10) |
|---|---|---|---|---|---|---|---|
| **T02** Cursos | (a) **pertenencia docente** `GET /api/courses/{courseId}/teacher-membership?teacherId=` → `{isMember}` · (b) **padrón** `ROSTER_UPDATED` (altas/bajas con `enrolledAt`) · (c) **encuestas agregadas** con **conteo por estrella 1–5, abstenciones, dimensión (curso/contenido/plataforma) y `courseClosed`** · (d) `COURSE_CLOSED` | HU11 (inactivos sin eventos), HU12 (403/RLS), HU13 (CSAT), US-15 | REST vía Gateway (token de servicio) + `courses.events` | 🟡 SOLICITUD LISTA — **sin respuesta**, redactada con el **envelope viejo de 8 campos** y `course.events` | `CourseEventConsumer` ingesta crudo ✅ | **Damián (C3)** | (a) flag `reporting.membership.t02.enabled=false` → PROFESOR **denegado** (fail-closed), ADMIN opera · (b) padrón derivado de alumnos vistos en T03 + aviso "padrón no disponible" · (c) CSAT "sin datos" |
| **T03** Desafíos | `CHALLENGE_COMPLETED` con `studentId`, `courseId`, `resultado`, `tiempoResolucionSegundos`, `usoTutorIa`, `xpGanada` (ya en `ChallengeCompletedPayload`) · confirmar los valores posibles de `resultado` | HU11 (actividad, reprobación), US-15 (métricas base) | `challenges.events` | 🟡 ACUERDO v1 | Consumidor ✅ | **Valentina (C8)** | Es la única fuente firmada: los reportes arrancan con ella. |
| **T05** Prácticos | Entregas y resultados de prácticos (incluye tardías, PAR-19/20) | HU11 (reprobación práctica), US-15 | Kafka (topic a registrar con T11) | ⏳ PENDIENTE — **no existe solicitud** | Nada | **Damián (C5)** | Flag apagado; métricas solo con T03 y el reporte lo aclara. |
| **T10** Roadmap | Progreso/XP/nivel, **vidas agotadas**, agregados de promoción/abandono | HU11 (factor "vidas agotadas"), US-15 | `sandbox.events` + `GET /api/roadmap/courses/{courseId}/retention` | 🟡 EN CURSO | Consumidor ✅ (flag apagado) | **Luciano (C6)** | Factor "vidas agotadas" detrás de flag; promoción/abandono "no disponible". |
| **T08** Banco | Saldos/transacciones | (ningún reporte del S2) | `accounting.events` + REST | 🟡 ACUERDO parcial | Consumidor ✅ (flag apagado) | **Bruno (C9, solo la firma)** | No bloquea el S2 (backlog). |
| **T11** Notificaciones | Que `notifications.events` esté **materializado** y el payload de `STUDENT_AT_HIGH_RISK` y `EXPORT_READY` · si registran `THRESHOLD_BREACHED` | HU12 CA2, HU09, HU14 | `notifications.events` (outbox) | ✅ nombres ratificados (mapeo v3) · falta materialización | Ruteo del outbox por topic ✅ (V17) | **Mateo (C2)** | El outbox deja la fila `PENDING` y la publica cuando el topic exista. |
| **T01** Identidad | Headers del Gateway v3, auditoría, estado 2FA, secreto del Gateway | Todos | Gateway + `identity.audit.events` | ✅ CERRADO | ✅ | **Máximo** | #1657 y 06B-T3 pasan a "Necesita información". |
| **T07** LLM | `/api/llm/admin/*`: ruta, Gateway o Eureka, token y scopes | HU04–HU07 | REST | 🟡 EN CURSO | Clientes **stub** | **Máximo (C1)** | HU04/05 funcionan con stub; HU06/07 se cortan (D-09). |

### 3.2 · Tareas de contrato (HT01, se mandan en el CP0)

> **Regla:** cada responsable de contrato, en **su misma PR**, actualiza el `.md` del tema, la tabla de estado y **su fila** en la tabla de firmas (G4) de `CONTRATOS.md` (los tres lugares juntos, como exige ese archivo). Ana no edita el repo: lleva el seguimiento en Taiga (T-C).

| ID | Tarea | Dev | Tamaño | Evidencia de "hecho" |
|---|---|---|---|---|
| C1 | Confirmar con T07 la llamada a `/api/llm/admin/*` · **en la misma PR:** corregir el §6 del contrato de T07 como fachada (ex C4) y registrar que `MODEL_CHANGED` lo publica T07 (ex 05-T5) · filas de firma de **T07 y T01** | Máximo | M | `CONTRATOS_T07_SOLICITUD.md` actualizado + filas firmadas |
| C2 | **Reducida:** los topics de auditoría y notificaciones **ya están ratificados** (PR #47). Solo falta confirmar con T11 la materialización de `notifications.events`, el payload de `STUDENT_AT_HIGH_RISK`/`EXPORT_READY` y si registran `THRESHOLD_BREACHED` · fila de **T11** | Mateo | S | Fila de T11 firmada |
| C3 | **Rehacer la solicitud a T02 con el envelope de 6 campos y `courses.events`**, agregando distribución 1–5, abstenciones, dimensión, `courseClosed`, `COURSE_CLOSED` y el endpoint de pertenencia · fila de **T02** | Damián | S | `CONTRATOS_T02_SOLICITUD.md` v2 + respuesta |
| C5 | **Nueva:** primera solicitud a T05 (entregas/resultados prácticos, PAR-19/20) · fila de **T05** | Damián | S | `CONTRATOS_T05_SOLICITUD.md` creado y enviado |
| C6 | **Nueva:** seguimiento con T10 (payload de `sandbox.events`, vidas agotadas, agregados de retención) · fila de **T10** | Luciano | S | `CONTRATOS_T10_SOLICITUD.md` con respuesta |
| C8 | **Nueva:** confirmar con T03 los valores posibles de `resultado` en `CHALLENGE_COMPLETED` · fila de **T03** | Valentina | S | `CONTRATOS_T03_RESPUESTA.md` actualizado |
| C9 | **Nueva:** fila de **T08** (el acuerdo parcial ya está escrito) | Bruno | S | Fila de T08 firmada |
| T-C | **Nueva:** seguimiento en Taiga: una tarjeta por tema fuente con responsable y fecha tope en el CP2; se repasa en cada daily | Ana | S | Ningún `⛔ a completar` en `CONTRATOS.md` al CP2 (o el motivo en la tarjeta) |

> **Anonimato en la ingesta (C3, crítico):** si T02 publica cada respuesta de encuesta como un evento, `reporting.ingested_event` guardaría el payload y el `occurred_at` exacto de cada respuesta, lo que habilita correlación por tiempo (RF-ENC-04 lo prohíbe expresamente). Pedir a T02 **agregados** por curso y dimensión; si no pueden, el consumidor de encuestas **no** persiste el payload crudo (solo suma conteos).

### 3.3 · Parámetros que consumen otros (misma lámina)

T03, T05, T08 y T10 leen `GET /api/backoffice/parameters/{key}` con el scope `backoffice.parameters.read` (ya desplegado, PRs #51/#52). En el S2 no hay trabajo nuevo acá salvo **PAR-12** (§4.2) y la validación de **PAR-14** (07-T1).

---

## 4 · Historias de arrastre del Sprint 1

### 4.1 · HT08 (nueva) · Cierre del Sprint 1 — 2 SP · **CP0**

| ID | Tarea | Dev | Tamaño |
|---|---|---|---|
| H-01 | `feature/us-01-testcontainers`: rescatar **solo** `GlobalParameterServiceIntegrationTest` sobre `develop` (descartar el `jsonKafkaTemplate`: la auditoría ya va por outbox) en `feature/tema-12-us01-parameter-it` | Mateo | M (= 01-IT) |
| H-02 | Cerrar sin mergear: `feature/contratos-alineados-drive` (superada por el mapeo v3) y `feature/us-02-envelope` (superada por `DomainEventOutboxImpl` + `EventEnvelope`) · FE: `fix/admin-export-service-spec` y los 3 commits sueltos de `feature/tema-12-backoffice` (`develop` ya tiene una versión más estricta: `3899e9d` + `495e29b`) | Mateo | S |
| H-04 | `feature/mvp-s6-golden-set-runs`: **no mergear**; tag `archive/s6-golden-set-local` y borrar la rama | Bruno | S |
| H-05 | `feature/contexto-sprint1`: PR solo de `docs/Task/plan-mvp-sprint1-backoffice.md` y `auditoria-contratos-skillhub.md` (sin `Contexto.md`) · `fix/development-doc-real-workflow`: rebase y PR solo de `DEVELOPMENT.md` | Luciano | S |
| H-06 | PR #54 `release/v1.0.0 → main`: CI verde + revisión + merge + tag `v1.0.0` | Damián (dueño) · revisa Mateo | S |
| H-07 | Taiga coherente (§5 de `auditoria-sprint1.md`) | Ana (dentro de D4) | S |

### 4.2 · HU01-bis · Registro PAR-01..24 completo — 2 SP

| ID | Tarea | Dev | Tamaño | Gate |
|---|---|---|---|---|
| 07-T1 | Validar **PAR-14** con la forma oficial de Skill Hub (`backoffice-t07-evaluacion-llm-contract` v1): `{"average": 5, "dimension": 10}` (**clave `average`, no `promedio`**), ambas numéricas y `0 < average ≤ dimension ≤ 100`. **Incluye renombrar `promedio → average` en el seed (migración)**. Hoy `ParameterValueRules` solo valida "es un mapa" | Damián | S | — |
| P-12 | **PAR-12** (`{"initialLives":3,"maxLives":3}`): **CONFIRMADO — es del Backoffice** (Skill Hub `backoffice-t08-banco-contract` v1, lo consume T08). Seed en **V24** + regla `1 ≤ initialLives ≤ maxLives` + corregir `AGENTS.md` (hecho en PR #56). **Además: sembrar PAR-03/06/07/24** (también son nuestros según Skill Hub; hoy V2 los excluye) en V24 o una migración contigua | Damián | S | — |
| 01-IT | IT con Testcontainers de US-01 (ver H-01) | Mateo | M | — |
| #3331 | Solo lectura de parámetros para PROFESSOR en el FE (el backend ya permite leer con `PARAMETER_READERS`) | Bruno | S | — |

### 4.3 · HU04 · Proveedores LLM reales — 5 SP · solo ADMIN

| ID | Tarea | Dev | Tamaño | Gate |
|---|---|---|---|---|
| 04-T1 | Infraestructura del cliente HTTP a T07: `RestClient` administrado, auth según C1, `problem+json` → `LlmProviderException`/`LlmModelException`, timeout 3 s, reintento solo en GET, propagar `X-Request-Id`, un 401/403 de T07 nunca se traduce a 500. El stub queda como fallback con flag | Máximo | M | Arranca con WireMock sin esperar C1 |
| 04-T2 | Cliente real de proveedores y credenciales. La key viaja a T07; nunca se loguea ni se devuelve | Regina | M | Sobre 04-T1 |
| 04-T3 | Pantalla 09 conectada a `/api/backoffice/llm/...` | Regina | M | — |
| 04-T4 | Suite WireMock del cliente de proveedores: 200, 404, 409, 503, key enmascarada | Luciano | M | — |
| 04-T5 | Peer review de seguridad de credenciales y del cliente T07 | Bruno | S | — |
| 04-T6 | OpenAPI de la fachada de proveedores y modelos | Regina | S | — |

### 4.4 · HU05 · Modelos y conmutación reales — 5 SP

| ID | Tarea | Dev | Tamaño |
|---|---|---|---|
| 05-T1 | Cliente real de modelos evaluadores (listar, activo, desplegar, activar, borrar) sobre 04-T1 | Mateo | M |
| #276 | Modal de conmutación con advertencia (actual → nuevo, confirmación explícita, textos en español) | Mateo | S |
| 05-T3 | Pantalla 10 conectada (hoy sirve `MOCK_MODELS` desde `llm-models.service.ts`) | Mateo | M |
| 05-T4 | Suite WireMock de la activación: 200, 409 (no aprobado), 503 (T07 caído) | Luciano | M |
| 05-T5 | Contrato: `MODEL_CHANGED` lo publica T07; el Backoffice deja de emitir `ModelProviderChanged` (**queda dentro de C1**: mismo archivo de contrato) | Máximo | — |
| 05-T6 | Peer review de la fachada de modelos | Joaquín | S |
| 05-T7 | Specs de las pantallas 09 y 10 conectadas | Valentina | M |

### 4.5 · HT05 · Frontend: acceso por rol y deuda S1 — 5 SP

| ID | Tarea | Dev | Tamaño | Corrección |
|---|---|---|---|---|
| #3512 | Guards: **la rama ya está pusheada** (`feature/tema-12-admin-route-guards`, `8c2c82e`); solo falta abrir la PR y avisar a los dueños de las partes 03, 04, 06, 10 y 14 | Máximo | S | Antes decía "pushear `ecae5fd`" |
| #291 | Badge de frescura: **ya está re-aplicado con los fixes de la review** en `feature/mvp-s7-ingestion-ui` (`d175ca1`). Abrir la PR a `develop` y dejar el componente reutilizable para los reportes (§6.3) | Valentina | S | Antes decía "rehacer" |
| #1657 | Estado de 2FA y sesión | Regina | S | Gate T01 |
| 05-N1 | Migrar las partes 01, 02, 04 y 06 de `/api/administration` y `/api/reports` a `/api/backoffice/...` (hoy en `admin-api-url.ts`) y retirar el parche de `proxy.conf.backoffice-gateway.cjs`. **Los alias viejos del backend no se borran en el S2** (otros equipos pueden seguir leyendo por ahí) | Luciano | M | — |
| 05-N2 | Dashboard: ocultar accesos no permitidos a GESTOR y PROFESSOR | Valentina | S | — |
| 05-N3 | Specs de guards y de la vista de solo lectura | Mateo | M | — |
| 05-N4 | Peer review de guards, migración de rutas, badge y 2FA | Joaquín | S | — |

### 4.6 · HT06 · Outbox y borde del Gateway — 3 SP

| ID | Tarea | Dev | Tamaño | Corrección |
|---|---|---|---|---|
| 06B-T1 | Orden estricto por clave en `findReadyToPublish`: no tomar una fila si hay una `PENDING` más vieja **con la misma clave de partición** (`NOT EXISTS`). El outbox ya es genérico (auditoría, riesgo, export) y guarda esa clave en la columna `param_key`; una fila en `DEAD_LETTER` no debe bloquear su clave | Luciano | S | Generalizada |
| 06B-T2 | IT del orden (Testcontainers + Kafka): falla v1 y v2 no sale antes | Máximo | M | — |
| 06B-T3 | Verificar `GATEWAY_SHARED_SECRET` con el mecanismo que acuerde T01, sin romper el entorno local | Máximo | S | Gate T01 |
| 06B-T4 | **Redefinida:** el topic ya es `identity.audit.events` (PR #47). Falta pasarlo de **constante** (`TOPIC_AUDIT_EVENTS`, `DEFAULT_AUDIT_TOPIC`) a **propiedad** tipada | Máximo | S | Antes: "configurar el topic confirmado" |
| 06B-T5 | Peer review de concurrencia del outbox y del secreto del Gateway | Valentina | S | — |
| 06B-T6 | Documentar el orden por clave en el contrato del consumidor | Luciano | S | — |

---

## 5 · Contratos compartidos del Sprint 2 — S2-00 (Luciano) · **CP1, congelado**

> Un solo PR, chico, **sin lógica**: interfaces, DTOs, enums, OpenAPI esqueleto y rutas stub del FE. Lo revisan **Máximo y Mateo** (dos aprobaciones porque afecta a todos). Después del merge **no se cambia sin avisar en el canal** y con PR propia.

| Artefacto | Paquete / archivo | Lo implementa | Lo consumen |
|---|---|---|---|
| `ReportScopeResolver` (ADMIN → `ALL`, PROFESSOR → curso validado o 403) | `reporting/services/access` | Máximo (#311) | #310, HU13, US-15, HU09 |
| `TeacherMembershipPort` (T02) | `reporting/services/access` | Máximo (#311) | `ReportScopeResolver` |
| `CohortSummaryQuery` (lectura del read model por curso) | `reporting/services` | Damián (#303) | #310, #304, US-15 |
| `DataFreshnessProvider` + `DataFreshnessDto {asOf, stale, thresholdMinutes, sources[]}` | `reporting/services`, `reporting/dtos` | Valentina (10-M1) | Todo endpoint de reporte |
| `RiskLevel {RED, YELLOW, GREEN}` + `RiskFactor` | `reporting/entities` | Damián (#304) | #310, #313, US-15 |
| `ReportMetric`, `ReportDimension` (**sin `TEACHER`**) | `reporting/dtos/dynamic` | Joaquín (15-T1) | Bruno (15-T2), Luciano (15-T8) |
| `ReportRunRequestDto` / `ReportRunResponseDto` / `ReportTemplateDto` | `reporting/dtos/dynamic` | Bruno (15-T4), Joaquín (15-T3) | Luciano (15-T8) |
| `TeacherPanelResponseDto` | `reporting/dtos/panel` | Regina (#310) | Damián (#313) |
| `CsatKpiDto` (distribución, abstenciones, `insufficientSample`, `availableAfterCourseClose`) | `reporting/dtos/kpi` | Mateo (HU13) | Valentina (#322) |
| OpenAPI esqueleto de los endpoints nuevos (§6) | `docs/openapi` | cada dueño completa el suyo | FE |
| FE: modelos TS + rutas stub (`reports/teacher/:courseId`, `reports/kpis`, `reports/builder`, `alerts`) con `loadPlaceholder` | `features/admin/admin.routes.ts` y `data-access` | cada dueño cambia **solo su línea** | FE |

> **Por qué:** en el S1 el PR de contratos compartidos (S3) permitió que 8 slices avanzaran con mocks sin pisarse. Es la mitigación directa de "tareas que se pisaban" de la retro.

---

## 6 · Reporting del Sprint 2 (lo asignado)

### Flujo y dueño de cada tramo

```text
T03 / T02 / (T05, T10 con flag)
   │ Kafka — consumidores ya en develop
   ▼
reporting.ingested_event (append-only)                     ← ya existe (S7)
   │ proyector cada ≤ 5 min (#305 · Valentina)
   ▼
read models V18 (#303 · Damián): cohort_roster, student_activity_summary (+ columnas de riesgo)
   │ cálculo de riesgo (#304 · Damián) ──► STUDENT_AT_HIGH_RISK por outbox (#312 · Damián)
   ▼
ReportScopeResolver + RLS V19 (#311 · Máximo) ◄── TeacherMembershipPort (T02)
   │
   ├─► Panel docente      GET  /reports/courses/{courseId}/teacher      (#310 · Regina)  ─► FE #313 (Damián)
   ├─► KPIs CSAT          GET  /reports/courses/{courseId}/kpis · /reports/platform (HU13 · Mateo) ─► FE #322 (Valentina)
   ├─► Reportes dinámicos POST /reports/run · /reports/templates · GET /reports/metrics (US-15 · Bruno/Joaquín) ─► FE 15-T8 (Luciano)
   ├─► Alertas (stretch)  /reports/alert-thresholds · /reports/alerts   (HU14 · Regina)
   └─► Export (stretch)   /reports/exports ─► EXPORT_READY por outbox   (HU09 · Damián)
Todas las respuestas llevan DataFreshnessDto (10-M1 · Valentina)
```

> Rutas bajo `${app.api.private-path}` = `/api/backoffice`. Roles del Gateway v3: `ADMIN`, `GESTOR`, `PROFESSOR`, `STUDENT`, `MS`.

### 6.1 · HU11 #25 · Read models y riesgo por cohorte — 5 SP

> Ramas: `feature/tema-12-hu11-read-model` (#303, **CP2**) · `feature/tema-12-hu11-projector` (#305, CP3) · `feature/tema-12-hu11-risk` (#304 + #312, CP3).

| ID | Tarea | Dev | Tamaño |
|---|---|---|---|
| #303 | `V18__reporting_cohort_read_model.sql`: `cohort_roster(course_id, student_id, enrolled_at, source)` y `student_activity_summary(course_id, student_id, last_activity_at, attempts, passed, failed, lives_exhausted, risk_level, risk_factors, risk_computed_at, previous_risk_level)` con índices por `course_id`. Entidades + `CohortSummaryQuery` | Damián | M |
| #305 | Proyector desde `ingested_event` (T03 `CHALLENGE_COMPLETED`, T02 `ROSTER_UPDATED`; T05/T10 detrás de flag), idempotente, con checkpoint (V26 si hace falta), cada ≤ 5 min (propiedad tipada, siempre < PAR-23) | Valentina | L |
| #304 | Clasificador de riesgo puro + recálculo. Umbrales en `@ConfigurationProperties("reporting.risk")`. Factor "vidas agotadas" detrás de `reporting.risk.lives-exhausted.enabled=false` | Damián | M |
| #306 | Partición de equivalencia y valores límite (tabla abajo) | Regina | M |
| #307 | Documentar reglas de riesgo y esquema del read model | Mateo | S |
| #308 | Peer review del modelado analítico | Máximo | S |

**Regla (Taiga HU11 + decisiones del grupo del 29/09) — se evalúa en este orden y gana la primera que aplica:**

| Orden | Estado | Condición |
|---|---|---|
| 1 | **RED** | inactividad **> 10** días **o** (≥ 3 intentos **y** reprobación **> 60 %**) **o** vidas agotadas (flag) |
| 2 | **YELLOW** | inactividad **5–10** días **o** (≥ 3 intentos **y** reprobación **40–60 %**) |
| 3 | **GREEN** | (≥ 3 intentos **y** aprobación **≥ 70 %**) **o** (< 3 intentos: las tasas no se evalúan) |
| 4 | **YELLOW** | **R-1:** cualquier otro caso (≥ 3 intentos con aprobación < 70 % y reprobación < 40 %) |

> ✅ **R-1 (decidido: YELLOW).** El hueco de la regla de Taiga (aprobación 60–70 % con menos de 5 días de inactividad) se clasifica como **YELLOW**: se prefiere avisar de más.
> ✅ **R-2 (decidido: mínimo 3 intentos).** Con menos de 3 intentos **no se calculan las tasas** (ni para bajar a RED/YELLOW ni como requisito de GREEN): el estado sale solo de la inactividad y de las vidas, y el panel muestra el factor "muestra insuficiente". Así un alumno que reprobó su único intento no queda en rojo.
> **Configuración tipada** `reporting.risk.*`: `red-inactivity-days=10`, `yellow-inactivity-days=5`, `red-failure-rate=60`, `yellow-failure-rate=40`, `green-approval-rate=70`, `min-attempts=3`, `lives-exhausted.enabled=false`.
> **Sin padrón (T02) no se ven los inactivos que nunca actuaron:** sin eventos no hay fila. Por eso el padrón de T02 es ruta crítica (§3).

**Casos que #306 tiene que cubrir (valores límite + decisiones):**

| Caso | Intentos | Aprobación / reprobación | Inactividad | Esperado |
|---|---:|---|---:|---|
| Límite de inactividad RED | 5 | 80 % / 20 % | 10 → 11 días | YELLOW → **RED** |
| Límite de inactividad YELLOW | 5 | 80 % / 20 % | 4 → 5 días | GREEN → **YELLOW** |
| Límite de reprobación RED | 100 | 40 % / 60 % → 39 % / 61 % | 1 día | YELLOW → **RED** |
| Límite de reprobación YELLOW | 100 | 61 % / 39 % → 60 % / 40 % | 1 día | YELLOW (R-1) → **YELLOW** (regla) |
| Límite de aprobación GREEN | 100 | 69 % / 31 % → 70 % / 30 % | 1 día | YELLOW (R-1) → **GREEN** |
| **R-1** | 3 (2 aprobados) | 67 % / 33 % | 2 días | **YELLOW** |
| **R-2** bajo el mínimo | 2 (0 aprobados) | 0 % / 100 % | 1 día | **GREEN** + "muestra insuficiente" |
| **R-2** en el mínimo | 3 (1 aprobado) | 33 % / 67 % | 1 día | **RED** |
| Precedencia | 10 (9 aprobados) | 90 % / 10 % | 14 días | **RED** (la inactividad gana) |
| Escenario 1 de Taiga | 5 | 40 % / 60 % | 14 días | **RED** |
| Escenario 2 de Taiga | 5 | 80 % / 20 % | 7 días | **YELLOW** |
| Escenario 3 de Taiga | 5 | 80 % / 20 % | 1 día | **GREEN** |

### 6.2 · HU12 #24 · Panel docente con RLS y alerta — 5 SP · DoD con **RLS verificado**

> Ramas: `feature/tema-12-hu12-access-rls` (#311, **CP2**) · `feature/tema-12-hu12-teacher-panel` (#310, CP3) · FE `feature/tema-12-hu12-teacher-panel-ui` (#313, CP4).

| ID | Tarea | Dev | Tamaño | Cambio vs propuesta |
|---|---|---|---|---|
| #311 | `ReportScopeResolver` + `TeacherMembershipPort` (adaptador T02 por Gateway con token de servicio; flag **fail-closed**) + `TenantContext` (`SET LOCAL app.current_course`) + **`V19__reporting_rls.sql`** con `ENABLE` **y `FORCE ROW LEVEL SECURITY`** (la app se conecta como dueña de las tablas) + guardia anti-comparación | Máximo | L | El puerto de pertenencia pasa de #310 a #311: es transversal (panel, KPIs, US-15, export) |
| #310 | `GET /api/backoffice/reports/courses/{courseId}/teacher`: alumnos con semáforo y factores, promedio **solo del propio curso**, `DataFreshnessDto`, < 2 s | Regina | M | Sin el adaptador de T02 |
| #312 | `STUDENT_AT_HIGH_RISK` por `DomainEventOutbox` **solo en la transición** a RED (misma transacción que el recálculo; payload con IDs, sin PII) a `notifications.events` | **Damián** | S | Pasa de Luciano a quien calcula el riesgo (evita tocar código ajeno) |
| #313 | Panel docente con semáforo (color **y** texto, teclado, WCAG AA) y badge de frescura | Damián | M | — |
| #314 + #315 | Suite de integración HU12 (Testcontainers PostgreSQL; H2 no soporta RLS): A→A 200, A→B 403, sin pertenencia 403, ADMIN 200, `ALL` solo ADMIN, anti-comparación, evento emitido una sola vez en la transición | **Luciano** | L | #315 pasa de Damián a Luciano (Damián es autor de #312) |
| 12-T9 | Specs del panel docente | Joaquín | S | — |
| #316 | Página de wiki "G06 - Reportes y panel docente": endpoints, política RLS explicada y aviso de riesgo (con ejemplos que le pasan Regina y Máximo) | Ana (wiki) | S | Pasa del repo a la wiki (D-13) |
| #317 | Peer review de seguridad RLS (crítico) | Mateo | S | — |

### 6.3 · HU10-bis · Frescura ≤ 15 min en cada reporte — 3 SP

| ID | Tarea | Dev | Tamaño | Cambio vs propuesta |
|---|---|---|---|---|
| 10-M1 | **Redefinida:** `DataFreshnessProvider` que arma `DataFreshnessDto` con `IngestionStatsQuery` y PAR-23 **al leer**, según las fuentes que declara cada reporte (panel: T03+T02; KPIs: T02; US-15: según métricas) | Valentina | M | Ya no es un `@Scheduled` que marca `isStale` |
| 10-M2 | **Redefinida:** tests del proveedor de frescura (14/15/16 min, fuente sin eventos, PAR-23 modificado) **y del proyector #305** | Regina | M | Cubre #305, que en la propuesta no tenía tester |

### 6.4 · US-15 · Reportes docentes dinámicos — fase 1 **Must** (5 SP) · fase 2 **Should** (3 SP)

> Ramas: `feature/tema-12-us15-catalog-templates` (Joaquín, CP3) · `feature/tema-12-us15-engine` (Bruno, CP4) · FE `feature/tema-12-us15-builder` (Luciano, CP5).
> **Invariantes (se revisan en toda PR de US-15):** métricas y dimensiones de lista blanca (enum), **sin expresiones libres ni SQL concatenado**; sin dimensión docente; PROFESOR solo sus cursos (403 si pide uno ajeno); métricas de encuesta solo agregadas y con PAR-18 + curso cerrado; `DataFreshnessDto` en la respuesta; paginado y rango de período máximo.

| ID | Tarea | Dev | Tamaño | Cambio vs propuesta |
|---|---|---|---|---|
| 15-T1 | Catálogo de métricas (tabla abajo) con su fuente y disponibilidad según el estado del contrato; `GET /reports/metrics` | Joaquín | M | Se agrega fuente y disponibilidad |
| 15-T3 | CRUD de plantillas y favoritas, solo del dueño: `V20__reporting_report_template.sql` (`owner_id, course_id, config jsonb, is_favorite`) | Joaquín | M | — |
| 15-T2 + 15-T4 | **Motor + `POST /reports/run`** (con `templateId` o `config`) con los invariantes, ejecutado dentro de `ReportScopeResolver` + RLS | **Bruno** | XL | **Consolidadas** (antes Bruno + Mateo en el mismo servicio) |
| 15-T5 | Tests del motor: RLS, anti-comparación, anonimato, lista blanca (métrica desconocida → 400) | Máximo | L | — |
| 15-T6 | OpenAPI de metrics/templates/run: **la escribe cada dueño en su endpoint** (Joaquín en 15-T1/15-T3, Bruno en 15-T2/T4) | Joaquín · Bruno | — | Antes era de Ana; la OpenAPI está en el código (`@Operation` + `docs/openapi`) |
| 15-T7 | Peer review de seguridad del motor | Valentina | S | — |
| 15-T8 | **Fase 2:** builder FE (métricas, filtros, período, columnas, agrupación, "Guardar plantilla", WCAG AA) | Luciano | L | Queda **Should**: la propuesta decía "entra completo" (§1) y a la vez "el builder cierra en el S3" (§2) |
| 15-T9 | **Fase 2:** specs del builder | Damián | M | — |
| 15-T10 | **Fase 2:** vista del builder y catálogo de métricas en la wiki | Ana (wiki) | S | Pasa del repo a la wiki (D-13) |
| 15-T11 | **Fase 2:** peer review del builder | Regina | S | — |

**Catálogo inicial (15-T1):**

| Métrica | Fuente | Disponible en el S2 |
|---|---|---|
| Actividad semanal, alumnos activos | T03 | ✅ |
| Tasa de aprobación / reprobación de desafíos | T03 `resultado` | ✅ |
| Tiempo promedio de resolución | T03 `tiempoResolucionSegundos` | ✅ |
| Uso del tutor IA (% de intentos con IA, consultas promedio) | T03 `usoTutorIa`, `cantidadConsultasIa` | ✅ |
| XP ganada (distribución) | T03 `xpGanada` (aproximada: el XP exacto es de T10 porque puede bajar retroactivamente) | ✅ con nota |
| Distribución de riesgo | HU11 | ✅ |
| CSAT: % satisfechos, % detractores, respuestas, abstenciones | T02 | 🔶 gate C3 |
| Promoción / abandono | T10 | 🔶 gate C6 |
| Entregas tardías | T05 | 🔶 gate C5 |

**Dimensiones permitidas:** `WEEK`, `CHALLENGE`, `RISK_LEVEL`, `COURSE` (PROFESOR: solo los propios). **Nunca** `TEACHER`; **nunca** `STUDENT` en métricas de encuesta.

### 6.5 · HU13 #111 · KPIs CSAT 5 estrellas con anonimato — 5 SP · Should

> Rama: `feature/tema-12-hu13-csat-kpis` (Mateo, CP4). **Consolidada** en un dueño (antes #319 Mateo, #320 Regina, #321 Bruno sobre el mismo servicio).

| ID | Tarea | Dev | Tamaño |
|---|---|---|---|
| #319/#320/#321 | `V22__reporting_survey_summary.sql` (conteos por estrella, abstenciones, dimensión, `course_closed`; **sin autor ni timestamp preciso**) · KPI-01/02 (% 4–5 y % 1–2 sobre respuestas emitidas; metas 80 % / 10 % como referencia) · abstenciones aparte (RF-ENC-10) · **PAR-18 + curso cerrado** para el PROFESOR (RF-ENC-13) · `GET /reports/courses/{courseId}/kpis` (PROFESOR propio, ADMIN) y `GET /reports/platform` (solo ADMIN: consolidado + desglose por curso **sin ranking**) | Mateo | L |
| #322 | Dashboard de KPIs con "muestra insuficiente" y "disponible al cierre del curso" | Valentina | M |
| #323 | Tests de anonimato: 4, 5 y 6 respuestas con PAR-18 = 5; curso abierto → sin puntajes; no-ADMIN en `/platform` → 403 | **Joaquín** | M |
| #324 | Políticas de privacidad y fórmulas | Joaquín | S |
| #325 | Peer review de privacidad | Damián | S |

### 6.6 · HU14 #34 · Alertas configurables — 3 SP · **Could (stretch)**

> Rama: `feature/tema-12-hu14-alerts` (Regina, CP5). **Vertical en un dueño** (antes #326 Regina, #330 Mateo, 14-T3 Luciano).

| ID | Tarea | Dev | Tamaño |
|---|---|---|---|
| #326/#330 | `V23__reporting_alerts.sql` (`alert_threshold(indicator, min_value, max_value, enabled)` + `alert`), CRUD solo ADMIN, evaluador periódico sobre indicadores de HU11/HU13, **alertas internas** (activa/resuelta). **No emite `THRESHOLD_BREACHED`** (D-08) | Regina | L |
| 14-T3 | Panel de umbrales + lista de alertas activas | Regina | M |
| 14-T4 | Tests: en el límite, 1 punto abajo, no-ADMIN 403 | Máximo | M |
| 14-T5 | Sección de la wiki con el catálogo de umbrales | Ana (wiki) | S |
| 14-T6 | Peer review | Joaquín | S |

### 6.7 · HU09 #20 · Exportación asíncrona — 5 SP · **Could (stretch)**

> Rama: `feature/tema-12-hu09-export` (Damián, CP5). Damián implementó las export tools de la slice 07 en el S1 (`ff33671`).

| ID | Tarea | Dev | Tamaño |
|---|---|---|---|
| 09-T1 | `V25__reporting_export_job.sql` + `POST /reports/exports` (desde una plantilla de US-15 o el panel) → job asíncrono → CSV → `EXPORT_READY` por outbox → `GET /reports/exports/{id}/download`. Hereda **todos** los invariantes (scope, anti-comparación, anonimato) | Damián | L |
| 09-T2 | Conectar las export tools de la slice 07 al export asíncrono | Damián | S |
| 09-T3 | Tests (scope, anonimato, evento una sola vez) | Bruno o Joaquín (quien libere primero su gate) | M |
| 09-T4 | Sección de la wiki del export y de `EXPORT_READY` | Ana (wiki) | S |

### 6.8 · HU06/HU07 (gate C1) y HU08-bis (gate contrato)

| ID | Tarea | Dev | Tamaño | Gate |
|---|---|---|---|---|
| #3537 | Fachada del perfil de calibración institucional sobre T07 | Joaquín | M | C1 |
| #3538 | Pantalla del perfil de calibración (parte 12) | Joaquín | M | C1 |
| #3539 | Fachada de corridas (crear, listar, detalle; `maeFinal`, `maxIndividualError`, veredicto de T07) | Bruno | M | C1 |
| #3540 | Pantalla de corridas (parte 13) | Bruno | M | C1 |
| 07-T2 | Estado de calibración del modelo activo (veredicto y deriva, leídos de T07) | Bruno | M | C1 |
| #284 | Indicador de veredicto/deriva y banner de conmutación | Valentina (**en Taiga figura Joaquín: corregir**) | S | C1 |
| #286 | T07 calcula MAE y veredicto; el Backoffice gobierna PAR-14 | Bruno | S | C1 |
| 06-T5 / 07-T4 | Tests WireMock de calibración y de estado (la parte de PAR-14 de 07-T4 **no** tiene gate) | Máximo | M / M | C1 |
| 06-T6 · 06-T7 · 06-T8 · 07-T6 | Diagrama de secuencia (Draw.io + wiki) · review · OpenAPI · review | Ana · Regina · Joaquín · Mateo | S | C1 |
| 08-T1 | Consumidores de `llm.events` (T07) y de T05 con flag, dedup y DLT (mismo patrón que `RawEnvelopeIngestor`) | Valentina | M | Contrato T07/T05 |
| 08-T3 | IT: nuevo, duplicado, malformado → DLT, flag apagado | Bruno | M | Ídem |
| 08-T4 · 08-T5 | Mapeo de contratos · review | Valentina · Luciano | S | Ídem |

### 6.9 · HT07 · Wiki de G06, diagramas y demo — 3 SP (Ana, D-13)

> La cátedra pide documentar cada tema en la wiki de Taiga (`guia-doc-proyecto-por-grupo`, plantilla `template-proyecto-por-grupo`): páginas **"G06 - TEMA"** con descripción, historias enlazadas, diagramas en Draw.io en orden **DER → BPMN → Clases → Estados → Secuencias → Microservicios** con su explicación, y endpoints con ejemplos. **G06 no tiene ninguna página todavía.**

| ID | Tarea | Dónde | Tamaño |
|---|---|---|---|
| D4 | Taiga coherente (H-07), carga del S2 y acta de la retro | Taiga | S |
| W-1 | **Nueva:** páginas del Sprint 1: "G06 - Parámetros globales y administración" y "G06 - Gobernanza LLM y contratos de lectura" | Wiki + Draw.io | L |
| D1 | Secuencia del cambio de parámetro (va en la página de parámetros) | Draw.io + wiki | S |
| D5 | **Nueva:** diagrama de microservicios "fuentes de datos → reportes" (§3.1) | Draw.io + wiki | S |
| #316 · 15-T10 | Página "G06 - Reportes y panel docente" (§6.2, §6.4) | Wiki | S · S |
| D-DEMO | Guion + checklist E2E (revisa Luciano) | Wiki o Drive | S |

**Salen de Ana (D-13):** C4 y 05-T5 → Máximo (dentro de C1) · C7 → cada responsable completa su fila de firma · 15-T6 → Joaquín y Bruno (OpenAPI en el código) · **D3 → DoD**: cada dueño actualiza `docs/backend/docs` y su OpenAPI en la misma PR del cambio.
**Insumos:** cada dueño de historia le pasa a Ana tablas para el DER y ejemplos reales de request/response, y revisa su sección (el detalle está en `dev-09.md`).

---

## 7 · Reserva de Flyway (D-12)

> `develop` llega a **V17** (V9–V13 y V16 quedaron reservadas y sin uso en el S1; **no se reutilizan**). `spring.flyway.out-of-order=true` permite mergear en cualquier orden, pero **cada número tiene un solo dueño**.

| Versión | Contenido | Dueño | CP |
|---|---|---|---|
| **V18** | `reporting_cohort_read_model` | Damián | CP2 |
| **V19** | `reporting_rls` (políticas + `FORCE`) sobre V18 | Máximo | CP2 |
| **V20** | `reporting_report_template` | Joaquín | CP3 |
| **V21** | `reporting_source_contract_v3` (corrige topics del seed de V14) | Joaquín | CP1 |
| **V22** | `reporting_survey_summary` (+ su política RLS) | Mateo | CP4 |
| **V23** | `reporting_alerts` (+ RLS si tiene `course_id`) | Regina | CP5 |
| **V24** | `global_parameter_par12` (solo si se confirma P-12) | Damián | CP2 |
| **V25** | `reporting_export_job` | Damián | CP5 |
| **V26** | `reporting_projection_checkpoint` (si #305 lo necesita) | Valentina | CP3 |

> **Regla:** toda tabla nueva de `reporting` con `course_id` trae su política RLS **en la misma migración**, copiando el patrón de V19.
> **V21 (arrastre S1):** el seed de V14 todavía tiene `courses.lifecycle`, `challenges.results` y `economy.transactions`; desde la PR #47 los topics reales son `courses.events`, `challenges.events` y `accounting.events`. La pantalla 14 de contratos muestra datos viejos. Una migración aplicada no se edita: se corrige con `UPDATE` en V21 (y se ajustan `ReadContractControllerTest` y `SourceContractRepositoryTest`, que usan los nombres viejos).

---

## 8 · Secuencia por checkpoints

| CP | Fecha | Backend (PR a `develop`) | Frontend | Coordinación |
|---|---|---|---|---|
| **CP0** | 29–30/09 | Higiene (H-01…H-06) · PR de S2-00 abierta | PR de #3512 y #291 (ramas ya terminadas) | Cada dev **valida su `dev-XX.md`** (§12) · se mandan C1, C2, C3, C5, C6 · Ana corrige Taiga |
| **CP1** | 01/10 | **S2-00 mergeado (congelado)** · 06B-T1 · V21 · 01-IT | 05-N1 | — |
| **CP2** | 02/10 | V18 (#303) · #311 + V19 · 04-T1 · 07-T1 (+V24 si P-12) | — | **Gate:** ¿respondieron T07 (C1) y T02 (C3)? Si no → flags y corte (§11) |
| **CP3** | 06/10 | #305 · #304 + #312 · #310 · 04-T2 · 05-T1 · 15-T1 + 15-T3 · 10-M1 | 04-T3 · 05-T3 · #276 | — |
| **CP4** | 08/10 | 15-T2/T4 · HU13 · suites #314/#315, 15-T5, #306, 10-M2, #323 · 06B-T2 | #313 · #322 · 05-T7 · 05-N3 | — |
| **CP5** | 09–11/10 | Stretch (HU14, HU09) · gates liberados | 15-T8 · 15-T9 · 14-T3 | Demo · E2E · retro |

**Reglas de PR (retro "Ordenar las PR"):**
1. **Una rama por slice**; la PR se abre **cuando la rama está terminada** (no PR "para ir viendo").
2. La descripción lleva: IDs de tarea, CA cubiertos, salida de `mvn -B clean verify` (en `develop` no hay CI de tests) o `npm run verify`, capturas si es FE, y los revisores de la matriz §9.
3. **Todo comentario de review va en GitHub** (en la PR o en la línea). Lo que se hable por WhatsApp se vuelca a la PR antes de aprobar.
4. Antes de pedir review: `git merge origin/develop` en la rama y volver a correr el verify (lección de la FE #114, que se mergeó 65 commits atrás de `develop`).

---

## 9 · Matriz de revisión cruzada (nadie testea ni revisa lo suyo)

| Código de… | Lo testea | Lo revisa |
|---|---|---|
| S2-00 (Luciano) | — (sin lógica) | **Máximo + Mateo** |
| 06B-T1, 06B-T6 (Luciano) | Máximo (06B-T2) | Valentina (06B-T5) |
| 05-N1 (Luciano) | Mateo (05-N3) | Joaquín (05-N4) |
| 15-T8 (Luciano) | Damián (15-T9) | Regina (15-T11) |
| 04-T1 (Máximo) | Luciano (04-T4) | Bruno (04-T5) |
| #311 (Máximo) · #310 (Regina) · #312 (Damián) | Luciano (#314/#315) | Mateo (#317) |
| 06B-T3/T4 (Máximo) · #3512 (Máximo) | Mateo (05-N3, para #3512) | Valentina (06B-T5) · Joaquín (05-N4) |
| 04-T2/04-T3 (Regina) | Luciano (04-T4) · Valentina (05-T7) | Bruno (04-T5) |
| 05-T1/#276/05-T3 (Mateo) | Luciano (05-T4) · Valentina (05-T7) | Joaquín (05-T6) |
| HU13 (Mateo) | Joaquín (#323) | Damián (#325) |
| 01-IT (Mateo, prueba código de Damián) | — | Regina |
| #303/#304 (Damián) | Regina (#306) | Máximo (#308) |
| #305 · 10-M1 (Valentina) | Regina (10-M2) | Máximo (#308) |
| #313 (Damián) | Joaquín (12-T9) | Mateo (#317) |
| #322 (Valentina) | — (specs propias) | Damián (#325) |
| 07-T1 · P-12 (Damián) | Máximo (07-T4, parte PAR-14) | Mateo (07-T6) |
| 15-T1/15-T3 (Joaquín) · 15-T2/T4 (Bruno) | Máximo (15-T5) | Valentina (15-T7) |
| V21 (Joaquín) | — | Valentina |
| HU14 (Regina) | Máximo (14-T4) | Joaquín (14-T6) |
| HU09 (Damián) | Bruno o Joaquín (09-T3) | Luciano |
| #3537/#3538 (Joaquín) · #3539/#3540/07-T2 (Bruno) | Máximo (06-T5, 07-T4) | Regina (06-T7) · Mateo (07-T6) |
| 08-T1 (Valentina) | Bruno (08-T3) | Luciano (08-T5) |
| #3331 (Bruno) · #291/05-N2 (Valentina) · #1657 (Regina) | Mateo (05-N3) | Joaquín (05-N4) |
| H-06 release v1.0.0 (Damián) | CI de `main` | Mateo |
| Páginas de wiki y diagramas de Ana | — | El dueño de la historia (que además le pasa los ejemplos reales) · D-DEMO: Luciano |
| Filas de firma de contratos (C1, C2, C3, C5, C6, C8, C9) | — | Ana verifica en Taiga (T-C) que ninguna quede `⛔ a completar` |

---

## 10 · Carga por dev (sin horas)

> Unidades relativas: S = 1, M = 2, L = 3, XL = 5. Sirven para **comparar** cargas, no para calcular tiempo. La disponibilidad sale de la tabla de capacidad del grupo (días, ausencias y dedicación).

| Dev | Integrante | Disponibilidad | Núcleo | Con gate | Stretch | Capas del núcleo |
|---|---|---|---:|---:|---:|---|
| 01 | Luciano Paz | Alta | 19 | 1 | — | BACK · FRONT · TEST · REV · DOC |
| 02 | Mateo Carballo Juarez | Alta | 16 | 1 | — | BACK · FRONT · TEST · REV · DOC |
| 03 | Damián Baigorria | Media (2 días de ausencia) | 15 | — | 4 | BACK · FRONT · TEST · REV · DOC |
| 04 | Joaquín Cortez | Baja | 12 | 5 | — | BACK · TEST · REV · DOC (FRONT con gate) |
| 05 | Valentina Maldonado | Media | 14 | 4 | — | BACK · FRONT · TEST · REV · DOC |
| 06 | Máximo Cerquatti | Alta | 17 | 5 | — | BACK · FRONT · TEST · REV · DOC |
| 07 | Regina Cerasulo | Media | 12 | 2 | 5 | BACK · FRONT · TEST · REV · DOC |
| 08 | Bruno Gianoli | Baja | 9 (incluye el XL de la ruta crítica) | 9 | — | BACK · FRONT · REV · DOC |
| 09 | Ana Paula Ducart | MSII (no codifica) | 10 | 1 | 2 | Taiga · wiki · Draw.io |

> **Numeración:** desde el Sprint 2 es corrida (01 a 09). En el Sprint 1 el 05 era Julieta, que ya no está en el equipo. Equivalencias con el S1: Valentina 06 → 05 · Máximo 07 → 06 · Regina 08 → 07 · Bruno 09 → 08 · Ana 10 → 09.

**Lectura de la tabla:**
- **Ana** no tiene tareas en los repos (D-13): su núcleo es la wiki de G06, que es un entregable de la cátedra que hoy falta.
- **Bruno** queda con la ruta crítica (motor de US-15) **sin otra tarea grande de núcleo**. Si C1 no llega, sus 9 unidades con gate se liberan y toma 09-T3. En la propuesta tenía 148 % con la ruta crítica.
- **Máximo** deja de ser "el tester del equipo" (la propuesta le daba la mayor carga de TEST): mantiene los tests de seguridad (su especialidad) y #323 pasa a Joaquín.
- **Luciano** tiene el núcleo más alto porque S2-00 dura un solo checkpoint y habilita a todos; no tiene stretch.

---

## 11 · Orden de corte (si la realidad no acompaña)

1. **Gates vencidos en el CP2:** HU06/HU07 (salvo 07-T1), 08-T1/08-T3, #1657, 06B-T3 → quedan con stub/flag y pasan a "Necesita información" en Taiga.
2. **Stretch:** HU09 → HU14.
3. **#322** (el backend de HU13 queda con OpenAPI y tests).
4. **15-T8/15-T9** (fase 2 de US-15) → Sprint 3, con la fase 1 cerrada.

**Nunca se corta:** HT08, HU01-bis, HU04, HU05, HT01, HT05, HT06, HU11, HU12, HU10-bis y US-15 fase 1.

---

## 12 · Control humano del plan (retro "Revisar la planificación")

Antes del CP1 **cada dev** confirma estos 5 puntos sobre su `dev-XX.md` (comentario en la PR del plan o en su historia de Taiga):

- [ ] Todas mis tareas son del Tema 12 (ninguna es de otro grupo: T01, T02, T07, T09, T10…).
- [ ] Ningún archivo que voy a tocar es de otro dev (§5, §7 y la sección "Archivos" de mi `dev-XX.md`).
- [ ] Mis dependencias están en S2-00 o tienen un gate con fallback.
- [ ] Sé qué CA cubro y quién me testea y revisa (§9).
- [ ] Si una tarea mía tiene gate, sé qué hago en el CP2 si no llega el contrato.

---

## 13 · Riesgos

| # | Riesgo | Impacto | Mitigación |
|---|---|---|---|
| R1 | **T02 no responde** (pertenencia, padrón, encuestas) | HU12 sin PROFESOR, HU11 sin inactivos, HU13 sin datos | C3 en el CP0; flag fail-closed; demo con ADMIN y datos de T03; escalar a la cátedra en el CP2 |
| R2 | T07 no confirma `/admin/*` | HU04/05 con stub, HU06/07 cortadas | 04-T1 con WireMock desde el inicio |
| R3 | RLS mal configurada (la app es dueña de las tablas) | Fuga entre cursos | `FORCE ROW LEVEL SECURITY` + suite #314 en PostgreSQL real + review crítica #317 |
| R4 | Motor de US-15 con SQL dinámico | Inyección / fuga de datos | Lista blanca por enum, parámetros bind, 15-T5 y 15-T7 |
| R5 | Anonimato de encuestas roto por la ingesta cruda | Incumple RF-ENC-04 | Agregados de T02; no persistir respuestas individuales (§3.2) |
| R6 | Colisión de Flyway | Migración rota en `develop` | §7 |
| R7 | Archivos protegidos del FE (`angular.json`, `package*.json`, `tsconfig*`, `.github/**`) | PR rechazada por el CI del FE | No tocarlos |
| R8 | Carga alta en Luciano, Mateo y Máximo | Atrasos | Stretch solo con núcleo mergeado; corte §11 |

---

## 14 · DoD (igual para los 9)

- **Tarea:** `mvn -B clean verify` en verde (tests, Checkstyle, PMD 3.26, JaCoCo ≥ 0,90) pegado en la PR · FE: `npm run verify` sin `ng build` local · código, comentarios, `@DisplayName` y mensajes de error en **inglés** · commits BE en español, FE en inglés · sin `var`, sin `@Autowired`, sin FQCN, sin `@Data` en entidades · `ErrorApi` en errores · `@PreAuthorize` en cada endpoint.
- **Historia:** CA cumplidos · Testcontainers donde hay BD real, RLS o eventos · autorización 200/403 con roles del Gateway v3 · **RLS verificado** en HU11/12/13/US-15/HU09 · `DataFreshnessDto` en cada reporte · **OpenAPI y `docs/backend/docs` actualizados en la misma PR del cambio** (absorbe la ex D3) · ejemplos reales entregados a Ana y su sección de wiki revisada · PR revisada según §9 · Taiga movida por su dueño.
- **Sprint:** suite completa en verde en `develop`, Taiga sin estados incoherentes, demo y retro.
