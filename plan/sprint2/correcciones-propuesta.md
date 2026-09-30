# Correcciones a la propuesta del Sprint 2

> **Qué es:** la lista de cambios que se hicieron sobre la propuesta unificada del grupo (`sprint2/tareas-sprint2.md` y `sprint2/dev-*.md`, que **se conservan sin tocar**) para llegar al plan corregido (`tareas-sprint2.md` y `dev-XX.md` de esta carpeta).
> **Criterio:** se respetó lo que el grupo decidió (alcance máximo, US-15 como prioridad de la cátedra, Opción A, regla de riesgo de `uh/US-11.md`, reserva de Flyway) y se corrigió solo lo que tiene **evidencia** en el código, en Taiga, en el PRD o en los contratos.
> **Lo bueno de la propuesta, que se mantiene:** matriz de revisión cruzada, reserva de Flyway, gates por contrato con flags, orden de corte explícito, regla de riesgo con umbrales en configuración tipada, US-15 con lista blanca de métricas y "sin expresiones arbitrarias".

---

## 1 · Tareas obsoletas o mal descriptas (ya hechas o superadas)

| # | En la propuesta | Evidencia | Corrección |
|---|---|---|---|
| 1 | **06B-T7** "Peer review de las PRs abiertas #47, #48 y #50" | Las tres se mergearon el **29/09** (15:13–15:30) | Se elimina |
| 2 | **06B-T4** "Configurar el topic de auditoría confirmado (C2)" | La PR #47 ya dejó `identity.audit.events` en ambos publishers | Se redefine: pasar el topic de constante a propiedad |
| 3 | **C2** "Confirmar con T11 los topics de auditoría y notificaciones y el `eventType` de `StudentAtHighRisk`" | `CONTRATOS_MAPEO_TOPICS.md` (v3) ya ratificó `identity.audit.events`, `notifications.events` y `STUDENT_AT_HIGH_RISK` | Se reduce a confirmar la materialización y el payload |
| 4 | **#291** "Rehacer el badge de frescura revertido en la PR #99" | El badge **ya está re-aplicado con los fixes** en `feature/mvp-s7-ingestion-ui` (`d175ca1`, 533 líneas, sin conflictos) | Solo abrir la PR |
| 5 | **#3512** "Pushear `ecae5fd`, abrir la PR" | La rama ya está en el remoto (`feature/tema-12-admin-route-guards`, `8c2c82e`) | Solo abrir la PR |
| 6 | **10-M1** "Monitor `@Scheduled` que marca `isStale` en `IngestionCounter`" | En `develop` la frescura **no se guarda: se calcula al leer** (Javadoc de `IngestionCounter`, `SourceIngestionStatsDto.stale`) | Se redefine: `DataFreshnessDto` en cada reporte, calculado al leer |
| 7 | **T10-1** "Consumidor de T10 (`sandbox.events`)" | `SandboxEventConsumer` **ya existe** en `develop` con flag | Lo que falta es la proyección, que depende del contrato → backlog |
| 8 | **T08-1** "Verificar/completar el adapter de replay REST de T08" | `EconomyTransactionConsumer` ya existe con flag; ningún reporte del S2 usa saldos | Backlog |

## 2 · Tareas que contradicen un contrato ratificado

| # | En la propuesta | Evidencia | Corrección |
|---|---|---|---|
| 9 | **#330** "`ThresholdBreached` (baja → alerta)" | `CONTRATOS_MAPEO_TOPICS.md`: **"No se emiten `DATA_STALE_DETECTED` ni `THRESHOLD_BREACHED`"** | HU14 con alertas internas; C2 pregunta si T11 lo registra |
| 10 | **B-AL** consumidor de `llm.budget.events` | Ese topic **no está registrado en T11** (mismo documento: "todavía no registrada con T11") | Backlog hasta que T11 lo registre y T07 firme el payload |
| 11 | **#320** "verificar si corresponde a un PAR: hay conflicto con PAR-18" | PRD: **PAR-18 = "Umbral mínimo de respuestas por curso para exponer resultados de encuesta al PROFESOR (RF-ENC-13)"** | No hay conflicto: el umbral **es** PAR-18 y se lee del registro |

## 3 · Brechas contra el PRD

| # | Qué faltaba | Requisito | Corrección |
|---|---|---|---|
| 12 | Mostrar resultados de encuesta **solo al cierre del curso** | RF-ENC-13 | Agregado a HU13 y a los invariantes de US-15 |
| 13 | Informar **abstenciones** fuera del denominador | RF-ENC-10 | Agregado a HU13 |
| 14 | CSAT como **distribución** (% 4–5 y % 1–2), no como promedio | KPI-01/02 | Agregado a HU13 y a la solicitud a T02 (C3) |
| 15 | Riesgo de **romper el anonimato por la ingesta cruda** (timestamp exacto por respuesta) | RF-ENC-04 | Pedido de agregados a T02 + regla de no persistir respuestas individuales |
| 16 | Métrica de **uso de IA** (el PRD la nombra en Analítica/Reporting) | Tabla 10 | Agregada al catálogo de US-15 con datos de T03 |

## 4 · Arrastre del Sprint 1 que no estaba en la propuesta

| # | Qué faltaba | Evidencia | Tarea nueva |
|---|---|---|---|
| 17 | Test de integración de US-01 en una rama sin PR | `feature/us-01-testcontainers` (Mateo) | H-01 / 01-IT |
| 18 | Ramas que hay que cerrar para que nadie las mergee por error | `us-02-envelope`, `contratos-alineados-drive`, `mvp-s6-golden-set-runs`, FE `fix/admin-export-service-spec` | H-02, H-04 |
| 19 | Seed de contratos con topics viejos | V14: `courses.lifecycle`, `challenges.results`, `economy.transactions` | V18 de la release v1.0.0 (PR #58, Mateo); el V21 de Joaquín quedó obsoleto |
| 20 | PAR-12 sin sembrar pese a estar CONFIRMADO en `PARAMETROS.md` | V2 lo excluye; `AGENTS.md` lo da como externo | P-12 (decisión del grupo) |
| 21 | Validación real de PAR-14 | `ParameterValueRules` solo valida "es un mapa" | Se mantiene 07-T1, pero **sin gate**; ya la hizo Mateo en la PR #56 |
| 22 | Solicitud a T05 | No existe `CONTRATOS_T05_SOLICITUD.md` | C5 |
| 23 | Solicitud a T02 desactualizada | Envelope de 8 campos y `course.events` | C3 reescrita |
| 24 | Tabla de firmas vacía | `⛔ a completar` en todas las filas de `CONTRATOS.md` | Cada responsable completa su fila en su PR (C1, C2, C3, C5, C6, C8, C9) · seguimiento de Ana en Taiga (T-C) |
| 25 | Release del Sprint 1 abierta | PR #54 | H-06 |
| 26 | Taiga incoherente | 6 historias con estado y cierre contradictorios | H-07 dentro de D4 |

## 5 · Cambios estructurales (para no pisarse)

| # | En la propuesta | Problema | Corrección |
|---|---|---|---|
| 27 | Sin PR de contratos compartidos | Cada slice iba a inventar sus DTOs e interfaces | **S2-00** congelado en el CP1 (como S3 en el S1) |
| 28 | **HU12** repartida entre 7 devs (#310 Regina, #311 Máximo, #312 Luciano, #313 Damián, #314 Luciano, #315 Damián, 12-T9 Joaquín) | #312 hacía que Luciano tocara el recálculo de riesgo de Damián; el adaptador de T02 dentro de #310 duplicaba lo que necesitan KPIs, US-15 y export | #312 → Damián (dueño del riesgo) · adaptador T02 → #311 (capa de acceso) · #315 → Luciano (Damián pasa a ser autor del evento) |
| 29 | **US-15** con 15-T2 (Bruno) y 15-T4 (Mateo) en el mismo servicio | Dos devs sobre el mismo motor | Consolidado en Bruno |
| 30 | **HU13** con #319 (Mateo), #320 (Regina), #321 (Bruno) | Tres devs en el mismo servicio de KPIs | Consolidado en Mateo |
| 31 | **HU14** con #326 (Regina), #330 (Mateo), 14-T3 (Luciano) | Tres devs en una historia chica | Vertical en Regina |
| 32 | 06B-T1 "no tomar una fila si hay una PENDING más vieja de la misma `param_key`" | El outbox ya es genérico (auditoría, riesgo, export) | La regla es por **clave de partición** |
| 33 | Reserva de Flyway V18–V20 | Hacen falta 9 versiones | V18–V26 con dueño (§7 del plan; renumeradas el 30/09 porque V18 y V24 ya están ocupadas) |

## 6 · Carga y estimación

| # | En la propuesta | Problema | Corrección |
|---|---|---|---|
| 34 | Horas por tarea y capacidad en horas | El equipo decidió no estimar en horas (con IA engañan) | SP por historia + líneas de código efectivas por tarea, repartidas parejas entre los 8 que programan (`distribucion-pareja.md`) |
| 35 | Carga nominal 117 % con todo en el mismo nivel | No distingue lo que depende de otro equipo | Núcleo / con gate / stretch; el stretch arranca con el núcleo mergeado |
| 36 | **Bruno** 148 % **y** dueño de la ruta crítica (15-T2) | El riesgo que la misma propuesta marcaba como PC1 | Bruno queda con el motor como único núcleo grande; sale T08-1 y #321 |
| 37 | **Máximo** con la mayor carga de TEST del equipo | Rol de "tester", contrario a "los 8 que programan cubren las 5 capas" | #323 pasa a Joaquín; Máximo conserva los tests de seguridad |
| 38 | US-15 "entra completo" (§1) y "el builder FE cierra en el S3" (§2) | Contradicción interna | Fase 1 **Must**, fase 2 **Should** |

## 7 · Datos a corregir en Taiga (los carga Ana)

| # | Dato | Corrección |
|---|---|---|
| 39 | #284 asignada a **Joaquín** en Taiga; la propuesta la da a **Valentina** | Reasignar a Valentina |
| 40 | #3537 "Implementar casos del golden set" y #3539 "corridas y cálculo de MAE" | Renombrar a "fachada del perfil" y "fachada de corridas" (el MAE es de T07) |
| 41 | #18, #1628, #1629 cerradas pero con estado "New"/"In progress" | Reabrir y mover al Sprint 2 |
| 42 | #17, #28, #182 en "Done" sin cerrar | Cerrar cuando se mergeen #3331, #291 y #3512 |

## 8 · Rol de Ana (MSII) y numeración

| # | En la propuesta | Evidencia | Corrección |
|---|---|---|---|
| 43 | Ana con tareas que se entregan por PR en el repo: C4 y 05-T5 (contrato T07), 15-T6 (OpenAPI), 15-T10 (OpenAPI de la vista), D3 (`docs/backend/docs` y el sitio) | Ana **no tiene commits** en el repo del backend ni en el del frontend; su trabajo está en Taiga (creó la mayoría de las tareas del S1) | Ana trabaja solo en Taiga (tablero y wiki) y Draw.io (D-13). C4 y 05-T5 → Máximo dentro de C1 · C7 → cada responsable completa su fila · 15-T6 → Joaquín y Bruno (la OpenAPI está en el código) · D3 → DoD de cada PR |
| 44 | Sin tarea para la wiki de Taiga | La cátedra exige páginas "GXX - TEMA" con plantilla (`guia-doc-proyecto-por-grupo`); **G06 no tiene ninguna**, y 8 grupos ya tienen la suya | W-1 (páginas del S1), #316 y 15-T10 (página de reportes), D1 y D5 (diagramas); cada dueño de historia le pasa insumos y revisa su sección |
| 45 | Seguimiento de contratos sin dueño en Taiga | La tabla de firmas quedó vacía todo el S1 | T-C: una tarjeta por tema fuente con fecha tope en el CP2 |
| 46 | Numeración dev-1…dev-10 sin el 5 | Julieta (dev-5 del S1) ya no está | Numeración corrida dev-01…dev-09: Valentina 05, Máximo 06, Regina 07, Bruno 08, Ana 09 |

## 9 · Decisiones que **no** se tomaron por el grupo (quedan abiertas)

| # | Decisión | Opción recomendada | Alternativa |
|---|---|---|---|
| R-1 | Alumno con 60–70 % de aprobación y < 5 días de inactividad (no encaja en ningún estado de la regla de HU11) | **YELLOW** (conservador) | GREEN |
| R-2 | Mínimo de intentos para evaluar la reprobación | **3** (`reporting.risk.min-attempts`) | Sin mínimo |
| P-12 | Dueño de PAR-12 | Seguir a `PARAMETROS.md` (Backoffice, lo consume T10) y corregir `AGENTS.md` | Confirmar como externo y corregir `PARAMETROS.md` |
