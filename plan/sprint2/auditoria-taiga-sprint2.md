# Auditoría de Taiga del grupo 06 para el Sprint 2

> **Fecha:** 30/09/2026 · **Modo:** solo lectura (no se modificó nada en Taiga) · **Proyecto:** 1804026 · **Fuente:** MCP de Taiga, plantillas `template-epicas` y `template-hist-usuario` de la wiki, y `distribucion-pareja.md`.
> **Qué hay que hacer:** cada punto de este documento es una acción para que **el dueño de la tarea o Ana** la ejecute en Taiga (el plan dice que cada dueño mueve su tarjeta). Las referencias (#NN) son las de Taiga.

## 0 · Resumen de hallazgos

| # | Hallazgo | Evidencia |
|---|---|---|
| 1 | **El sprint "G06 - Sprint 2" existe (28/09 → 11/10) pero está vacío:** 0 historias y 0 tareas | Sprint 533585 |
| 2 | **Ninguna de las 5 épicas tiene descripción**: no usan la plantilla de épicas | Épicas #2 a #6 |
| 3 | **Ninguna historia ni tarea tiene etiquetas (tags)** | `tags: []` en las 18 historias y en las tareas revisadas |
| 4 | **Las 34 tareas de HU09, HU11, HU12, HU13 y HU14 no tienen responsable** y ninguna está en un sprint | `assigned_to: null` |
| 5 | **HU09 y sus 7 tareas están en el sprint de OTRO grupo** ("G01 - Sprint 1") | Historia #20, tareas #295 a #302 |
| 6 | **Hay 6 historias del Sprint 1 con estado incoherente** (cerradas en "New" o "In progress", o "Done" con tareas abiertas) | #18, #1628, #1629, #17, #28, #182 |
| 7 | **Las historias HU05, HU06, HU07, HU09, HU13 y HU14 están desactualizadas** respecto de lo decidido (Opción A con T07, T11, PAR-18) | Descripciones revisadas |
| 8 | **8 historias no tienen puntos** aunque su texto dice 3 o 5 | HU04, HU05, HU06, HU07, HU09, HU11, HU12, HU14 |
| 9 | **Solo falta una historia realmente nueva: los reportes dinámicos** (requisito de la cátedra). Todo lo demás del plan (deuda del frontend, outbox, Gateway, wiki, cierre del Sprint 1) se ubica como tareas en historias que ya existen | Ninguna historia cubre reportes dinámicos |
| 10 | **El plan del grupo cita mal una tarea de HU14:** `#330` no es el evaluador (es documentación); el evaluador es `#327` | Tareas #326 a #331 |

## 1 · Épicas

Hay 5 épicas de G06. **No hace falta crear ninguna nueva.**

| Épica | Historias hoy | Acción |
|---|---:|---|
| #2 G06 - Parámetros Globales | 5 | **Completar con la plantilla** (objetivo, suposiciones, criterios, dependencias). No suma historias nuevas: reabre HU02 (#21), HT02 (#1630) y HT03 (#1631) |
| #4 G06 - Administración de la Plataforma | 1 | **Completar con la plantilla.** No suma historias nuevas |
| #3 G06 - Configuración de Modelos LLM y Golden Set | 4 | **Completar con la plantilla** dejando claro que el Backoffice es **fachada de T07** (Opción A) |
| #5 G06 - Observabilidad, Reportes y Panel de Riesgo | 5 | **Completar con la plantilla.** **Sumar:** las dos historias nuevas de reportes dinámicos (HU15 y HU16). **Renombrar** a "Reportes y Panel de Riesgo": la observabilidad de otros microservicios quedó fuera de alcance |
| #6 G06 - Contratos de Lectura e Ingesta | 3 | **Completar con la plantilla** con el estado de cada contrato (T02, T03, T05, T07, T08, T10, T11) |

**Contenido sugerido para cada épica (plantilla):**

| Sección | Qué poner |
|---|---|
| Objetivo | 1 o 2 líneas de valor (por ejemplo, en la #5: "el docente ve a sus alumnos en riesgo y reportes con datos de menos de 15 minutos, sin comparar docentes") |
| Suposiciones y restricciones | Suposiciones: los demás temas exponen sus lecturas. Restricciones: el Backoffice no tiene dominio propio; anonimato de encuestas (RF-ENC-04); Gateway autentica y Backoffice autoriza |
| Criterios a nivel épica | Flujo extremo a extremo posible, KPI de frescura ≤ 15 min, sin regresiones, documentación publicada en la wiki |
| Dependencias | Servicios, módulos, otros equipos, migraciones y flags (los de `tareas-sprint2.md` §3) |

## 2 · Historias

### 2.1 Mover al sprint "G06 - Sprint 2" (18 historias: todas las del Sprint 1 que tienen trabajo pendiente)

| Historia | Hoy | Acción | Motivo |
|---|---|---|---|
| #21 HU02 Propagación del cambio (outbox) | Sprint 1, "Done" y cerrada | **Reabrir y mover al Sprint 2** | Recibe el orden estricto del outbox por clave (06B-T1, 06B-T2, 06B-T6), que es el criterio CA4 de esta historia |
| #1630 HT02 Infra: scaffolding, compose y CI | Sprint 1, "Done" y cerrada | **Reabrir y mover** | Recibe el cierre de la release v1.0.0, la limpieza de ramas del backend y el secreto del Gateway |
| #1631 HT03 Documentación de diseño | Sprint 1, "Done" y cerrada | **Reabrir y mover** (renombrar a "Documentación de diseño: wiki y diagramas") | Recibe las páginas de wiki de G06, los diagramas, el guion de la demo y el orden de Taiga |
| #17 HU01 Parámetros globales | Sprint 1, "Done" sin cerrar | **Mover al Sprint 2** y poner en "In progress" | Tiene abiertas #3331 y la PR #56 |
| #18 HU04 Proveedores LLM | Sprint 1, cerrada en "New" | **Reabrir y mover** | Falta el cliente real hacia T07 |
| #26 HU05 Conmutación de modelos | Sprint 1, "New" | **Mover** | Falta el cliente real, la pantalla 10 real y el modal (#276) |
| #27 HU06 Golden set | Sprint 1, "New" | **Mover** (condicionada a T07) | Fachada de calibración |
| #29 HU07 Tolerancia PAR-14 y deriva | Sprint 1, "New" | **Mover** (condicionada a T07) | Indicador de deriva y PAR-14 |
| #28 HU10 Frescura | Sprint 1, "Done" sin cerrar | **Mover al Sprint 2** y poner en "In progress" | Falta #291 (badge) y la frescura en cada reporte |
| #182 HU03 Rol administrador | Sprint 1, "Done" sin cerrar | **Mover** y poner en "In progress" | Falta #3512 (guards) |
| #1628 HU08 Ingesta | Sprint 1, cerrada en "In progress" | **Reabrir y mover** | Faltan los consumidores de T07 y T05 con flag |
| #1629 HT01 Contratos | Sprint 1, cerrada en "New" | **Reabrir y mover** | Faltan las firmas de T02, T05, T07, T10, T11 |
| #1632 HT04 Frontend conexión | Sprint 1, "New" | **Mover** | Queda #1657 (2FA) |
| #25 HU11 Read model y riesgo | Backlog | **Mover** | Núcleo del sprint |
| #24 HU12 Panel docente | Backlog | **Mover** | Núcleo del sprint |
| #111 HU13 KPIs consolidados | Backlog | **Mover** | Should del sprint |
| #34 HU14 Umbrales | Backlog | **Mover como extra** (prioridad Could) | Solo con el núcleo mergeado |
| **#20 HU09 Exportación** | **"G01 - Sprint 1" (de otro grupo)** | **Sacar del sprint de G01 y moverla al Sprint 2 como extra** | Está en el sprint equivocado |

**No queda ninguna historia en el Sprint 1 con trabajo pendiente.** Las tareas ya terminadas siguen cerradas dentro de cada historia.

### 2.2 Modificar la descripción (siguen siendo correctas en forma, pero están desactualizadas)

| Historia | Qué cambiar |
|---|---|
| #26 HU05 | El evento `ModelProviderChanged` ya **no lo emite el Backoffice**: lo publica T07 (`MODEL_CHANGED`). La ruta es `/api/backoffice/llm/models/{id}/activate`, no `/api/administration/...`. Agregar que es **fachada de T07** |
| #27 HU06 | El golden set y el cálculo del error **los hace T07**; el Backoffice solo es fachada (perfil y corridas). Quitar "el sistema calcula el error promedio" del alcance propio |
| #29 HU07 | T07 calcula el error y el veredicto; el Backoffice **gobierna PAR-14** (forma `{"average", "dimension"}`, rango validado) y muestra veredicto y deriva. La deriva la emite T07 |
| #20 HU09 | Acotar a **CSV** en este sprint (PDF queda para el siguiente). El aviso `EXPORT_READY` sale por `notifications.events`. Rutas bajo `/api/backoffice/reports/exports`. Mantiene "Could" |
| #111 HU13 | La fuente de encuestas es **T02**, no T04. Agregar **PAR-18** como umbral, el **corte al cierre del curso** (RF-ENC-13), las **abstenciones** (RF-ENC-10) y la distribución 1 a 5. Ruta: `/api/backoffice/reports/platform` y `/courses/{id}/kpis` |
| #34 HU14 | **No se emite `THRESHOLD_BREACHED`** (T11 lo excluyó): las alertas son internas. Cambiar `GET /api/alerts` a la ruta del Backoffice |
| #25 HU11 | Agregar las decisiones **R-1** (el caso sin clasificar va a amarillo) y **R-2** (mínimo 3 intentos). Ajustar el BDD con 12 casos (tabla en `tareas-sprint2.md` §6.1) |
| #24 HU12 | Agregar que la pertenencia docente se consulta a T02 y **falla cerrada** si T02 no responde; el aviso se publica en `notifications.events` |
| #28 HU10 | Agregar que la frescura se calcula **al leer** y se informa en **cada reporte** (`DataFreshnessDto`) |

### 2.3 Puntos y prioridad (faltan en 8 historias)

| Historia | Puntos | Prioridad |
|---|---:|---|
| #18 HU04 | 5 | Must |
| #26 HU05 | 5 | Must |
| #27 HU06 | 5 | Should (condicionada) |
| #29 HU07 | 5 | Should (condicionada) |
| #20 HU09 | 5 | Could |
| #25 HU11 | 5 | Must |
| #24 HU12 | 5 | Must |
| #34 HU14 | 3 | Could |

### 2.4 Agregar (solo 2 historias nuevas, con la plantilla de historia de usuario)

| Historia nueva | Épica | Puntos | Prioridad | Contenido |
|---|---|---:|---|---|
| **HU15 · Reportes docentes dinámicos (motor y ejecución)** | #5 | 5 | Must | El PROFESOR arma un reporte eligiendo métricas, filtros, período y agrupación; lista blanca de métricas; sin dimensión docente; acceso solo a sus cursos |
| **HU16 · Constructor de reportes (frontend)** | #5 | 3 | Should | Pantalla para armar y guardar plantillas de reporte |

**No se crean** las historias HT05, HT06, HT07, HT08 ni las "bis" del plan. Son paquetes de trabajo del plan, no historias: sus tareas se ubican así en historias que ya existen.

| Paquete del plan | Dónde van sus tareas en Taiga |
|---|---|
| HT05 (frontend: acceso por rol y deuda) | **#1632 HT04** "Frontend: conexión al backend, guards y feedback" (05-N1 a 05-N4), más #3512, #291, #1657 y #3331 que ya existen |
| HT06 (outbox y Gateway) | **#21 HU02** (06B-T1, 06B-T2, 06B-T6), **#1630 HT02** (06B-T3, 06B-T5) y **#182 HU03** (06B-T4, topic de auditoría) |
| HT07 (wiki, diagramas, demo) | **#1631 HT03** (W-1, D1, D5, D-DEMO, D4) |
| HT08 (cierre del Sprint 1) | Sus tareas van a la historia a la que pertenece cada una: H-01 a **#17 HU01**, H-04 a **#27 HU06**, H-05 a **#1631 HT03**, H-06 y H-02 (backend) a **#1630 HT02**, H-02 (frontend) a **#1632 HT04**, H-07 a **#1631 HT03** |
| HU01-bis, HU10-bis, HU08-bis | **#17**, **#28** y **#1628** |

## 3 · Tareas

### 3.1 Mover al Sprint 2 y asignar (existentes, 11 del Sprint 1)

| Tarea | Hoy | Nueva asignación | Otros cambios |
|---|---|---|---|
| #276 modal de conmutación | Mateo | Mateo | Mover |
| #284 indicador de deriva | **Joaquín** | **Valentina** | Mover; condicionada a T07 |
| #286 documentar deriva y PAR-14 | Bruno | Bruno | Mover; condicionada a T07 |
| #291 badge de frescura | Valentina | Valentina | Mover; solo falta abrir la PR |
| #1657 estado de 2FA | **Regina** | **Luciano** | Mover; condicionada a T01 |
| #3331 solo lectura para PROFESSOR | **Bruno** | **Damián** | Mover |
| #3512 guards | Máximo | Máximo | Mover; solo falta abrir la PR |
| #3537 | Joaquín | Joaquín | **Renombrar** a "Fachada del perfil de calibración sobre T07" (hoy dice "Implementar casos del golden set") |
| #3538 | Joaquín | Joaquín | **Renombrar** a "Pantalla del perfil de calibración" |
| #3539 | Bruno | Bruno | **Renombrar** a "Fachada de corridas de calibración" (hoy dice "calcular MAE", que lo hace T07) |
| #3540 | **Bruno** | **Mateo** | Renombrar a "Pantalla de corridas de calibración"; condicionada a T07 |

### 3.2 Asignar y mover (existentes del backlog, hoy sin responsable)

| Historia | Tarea | Asignar a | Cambio |
|---|---|---|---|
| HU11 | #303 migración del read model | Damián | La migración será **V19** (V18 la ocupa la release) |
| | #304 algoritmo de riesgo | Damián | Incluir R-1 y R-2 en la descripción |
| | #305 job de recálculo | Valentina | Renombrar a "Proyector de read models cada 5 min o menos" |
| | #306 tests de partición | Regina | Usar los 12 casos de `tareas-sprint2.md` §6.1 |
| | #307 documentar reglas | Mateo | — |
| | #308 peer review | Máximo | — |
| HU12 | #310 endpoint del panel | Regina | Quitar "validación vía T02" de esta tarea: pasa al resolvedor de Máximo |
| | #311 RLS | Máximo | Agregar `FORCE ROW LEVEL SECURITY`, resolvedor de alcance y adaptador de T02 |
| | #312 notificación de riesgo alto | Damián | Renombrar a "Emitir `STUDENT_AT_HIGH_RISK` por outbox solo al pasar a rojo" |
| | #313 panel con semáforo | Luciano | — |
| | #314 tests de RLS | Luciano | — |
| | #315 tests de anti-comparación | Luciano | — |
| | #316 documentar endpoints | Ana | Cambiar a "Página de wiki de reportes y panel docente" |
| | #317 peer review de RLS | Mateo | — |
| HU13 | #319 agregación | Joaquín | Incluir distribución 1 a 5 y abstenciones |
| | #320 anonimato por umbral | Joaquín | El umbral **es PAR-18**, no un parámetro nuevo |
| | #321 endpoint ADMIN | Joaquín | Ruta `/api/backoffice/reports/platform` |
| | #322 dashboard de KPIs | Mateo | — |
| | #323 tests de anonimato | Regina | — |
| | #324 documentar privacidad | Joaquín | — |
| | #325 peer review | Damián | — |
| HU14 (extra) | #326 modelo y CRUD de umbrales | Regina | — |
| | #327 evaluador periódico | Regina | **No emite `THRESHOLD_BREACHED`**: alertas internas |
| | #328 panel de umbrales | Luciano | — |
| | #329 tests | Máximo | — |
| | #330 documentar catálogo | Ana | Pasa a la wiki |
| | #331 peer review | Joaquín | — |
| HU09 (extra) | #295 solicitud de exportación | Damián | **Sacar del "G01 - Sprint 1"** |
| | #296 generador en streaming | Damián | Acotar a CSV |
| | #297 enlace temporal y alcance | Damián | — |
| | #299 diálogo de exportación | Valentina | — |
| | #300 tests | Máximo | — |
| | #301 documentar flujo asíncrono | Ana | Pasa a la wiki |
| | #302 peer review | Luciano | — |

### 3.3 Agregar (tareas que no existen en Taiga)

| Historia | Tareas nuevas | Responsable |
|---|---|---|
| #17 HU01 | **PR #56** (PAR-14 `average`, PAR-12 y alineación con Skill Hub) · **01-IT** test de integración de US-01 · revisión de la PR #56 | Mateo · Mateo · Damián |
| #18 HU04 | **04-T1** cliente HTTP a T07 · **04-T2** cliente de proveedores · **04-T3** pantalla 09 conectada · **04-T4** suite WireMock · **04-T5** revisión de seguridad · **04-T6** OpenAPI | Máximo · Regina · Regina · Luciano · Bruno · Regina |
| #26 HU05 | **05-T1** cliente real de modelos · **05-T3** pantalla 10 conectada · **05-T4** suite WireMock · **05-T6** revisión · **05-T7** specs pantallas 09 y 10 | Mateo · Mateo · Luciano · Joaquín · Valentina |
| #27 HU06 | **06-T5** tests WireMock · **06-T6** diagrama de secuencia · **06-T7** revisión · **06-T8** OpenAPI | Máximo · Ana · Regina · Joaquín |
| #29 HU07 | **07-T2** estado de calibración del modelo activo · **07-T4** tests · **07-T6** revisión | Bruno · Máximo · Damián |
| #28 HU10 | **10-M1** frescura en cada reporte (`DataFreshnessDto`) · **10-M2** tests de frescura y del proyector | Valentina · Regina |
| #1628 HU08 | **08-T1** consumidores de T07 y T05 con flag · **08-T3** tests de integración · **08-T4** mapeo de contratos · **08-T5** revisión | Valentina · Mateo · Valentina · Luciano |
| #1629 HT01 | **C1** T07 y T01 · **C2** T11 · **C3** T02 · **C5** T05 · **C6** T10 · **C8** T03 · **C9** T08 (cada una con su fila de firma) · **T-C** seguimiento en Taiga | Máximo · Mateo · Damián · Damián · Luciano · Valentina · Bruno · Ana |
| #24 HU12 | **12-T9** specs del panel docente | Bruno |
| #111 HU13 | Corte al cierre del curso (RF-ENC-13) y abstenciones (RF-ENC-10) | Joaquín |
| HU15 (nueva) | **15-T1** catálogo de métricas · **15-T2** motor · **15-T3** plantillas · **15-T4** ejecución (`run`) · **15-T5** tests · **15-T6** OpenAPI · **15-T7** revisión de seguridad | Joaquín · Bruno · Joaquín · Bruno · Máximo · Joaquín y Bruno · Valentina |
| HU16 (nueva) | **15-T8** constructor · **15-T9** specs · **15-T10** wiki · **15-T11** revisión | Damián · Bruno · Ana · Regina |
| #1632 HT04 (reabierta) | **05-N1** migrar a `/api/backoffice` · **05-N2** dashboard por rol · **05-N3** specs de guards y solo lectura · **05-N4** revisión · **H-02** cerrar rama del frontend superada | Luciano · Valentina · Mateo · Joaquín · Mateo |
| #21 HU02 (reabierta) | **06B-T1** orden del outbox · **06B-T2** test del orden · **06B-T6** documentar el orden | Luciano · Regina · Luciano |
| #1630 HT02 (reabierta) | **06B-T3** secreto del Gateway · **06B-T5** revisión · **H-02** cerrar ramas del backend · **H-06** release v1.0.0 (PR #54) | Máximo · Valentina · Mateo · Damián |
| #182 HU03 | **06B-T4** topic de auditoría como propiedad | Máximo |
| #1631 HT03 (reabierta) | **W-1** páginas de wiki del Sprint 1 · **D1** secuencia del cambio de parámetro · **D5** diagrama de fuentes a reportes · **D-DEMO** guion de la demo · **D4** estados y carga de Taiga · **H-05** PR de documentación del Sprint 1 | Ana · Ana · Ana · Ana · Ana · Luciano |
| #27 HU06 | **H-04** archivar la rama del golden set local | Bruno |

### 3.4 Sacar o no crear

| Tarea | Acción | Motivo |
|---|---|---|
| **B-AL** (alerta de presupuesto LLM) | **No crear** | `llm.budget.events` no está registrado en T11 |
| **T10-1** (proyección de T10) | **No crear** | Falta el contrato con T10 |
| **T08-1** (replay de T08) | **No crear** | Ningún reporte del sprint usa saldos |
| **06B-T7** (revisar PRs #47, #48 y #50) | **No crear** | Ya se mergearon el 29/09 |
| **V21** (corregir topics del registro de contratos) | **No crear** | Ya lo hace la V18 de la release |
| **07-T1 y P-12 como tareas de Damián** | **No asignar a Damián** | Ya las hizo Mateo en la PR #56 |

### 3.5 Etiquetas (tags)

Ninguna tarea tiene etiquetas. Cargar en cada tarea una o más de las 14 definidas por la cátedra (Backend, Frontend, Testing, Base de Datos, DevOps, Documentación, Análisis, Diseño / UX-UI, Integración, Configuración, Seguridad, Investigación, Gestión, Otro). **La etiqueta de cada tarea está en `etiquetas-tareas.md`** (índice maestro) y en la columna *Etiquetas* de cada `dev-XX.md`. Las tareas existentes tienen además el tipo entre corchetes en el título (`[BACKEND]`, `[FRONTEND]`, `[TEST]`, `[DOCUMENTACION]`, `[REVISION]`); conviene mantener ese formato en las nuevas: `[G06] - [TIPO] - descripción`.

## 4 · Sprint "G06 - Sprint 2"

| Dato | Hoy | Acción |
|---|---|---|
| Fechas | 28/09 al 11/10 | Correctas |
| Historias | 0 | Mover las 18 del punto 2.1 y crear las 2 del punto 2.4 |
| Tareas | 0 | Mover las 11 del punto 3.1 y las 34 del 3.2, y crear las del 3.3 |
| Puntos | sin cargar | Núcleo de 48 puntos, más lo condicionado y los extras (ver `tareas-sprint2.md` §2) |

## 5 · Orden sugerido para ejecutarlo

1. **Ana, el primer día:** corregir los estados incoherentes (punto 2.1), sacar HU09 de "G01 - Sprint 1" y cargar las historias y tareas nuevas.
2. **Cada dueño:** asignarse sus tareas (puntos 3.1 a 3.3), cargar etiquetas y mover su tarjeta al abrir y al mergear la PR.
3. **Mateo y Ana:** actualizar las descripciones de las historias (punto 2.2) con la plantilla.
4. **Ana:** completar las 5 épicas con la plantilla (punto 1).
5. **Quien coordina:** revisar que no quede ninguna tarea del sprint sin responsable ni sin etiqueta.

## 6 · Límites de esta auditoría

- El MCP de Taiga devuelve **30 elementos por consulta**. Para completar el cuadro consulté por épica, por historia y por sprint; pero no revisé el contenido de las historias cerradas ni la descripción de #17, #18, #21, #28, #182, #1628 y #1629.
- No revisé los comentarios ni el historial de cambios de las tarjetas.
- Las asignaciones salen de `distribucion-pareja.md`; si el grupo cambia el reparto, hay que actualizar este documento.
