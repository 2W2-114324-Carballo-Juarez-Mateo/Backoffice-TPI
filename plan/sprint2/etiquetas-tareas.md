# Etiquetas de tipo de trabajo — Sprint 2

> **Para qué sirve:** que cada tarea quede identificada por el tipo de actividad (una o más etiquetas) y poder analizar después qué hizo cada integrante y cómo se distribuyó el trabajo. Las etiquetas son las definidas por la cátedra. **Cada dueño las carga en Taiga en su propia tarea**; este archivo es la referencia.
> **Dónde están:** en cada `dev-XX.md` (columna *Etiquetas* y nota al final de las tareas en viñetas) y acá, el índice completo.

## Etiquetas y criterio

| Etiqueta | Se usa cuando la tarea… |
|---|---|
| Backend | implementa lógica, endpoints, servicios o clientes del lado servidor |
| Frontend | implementa pantallas, componentes o lógica del cliente |
| Testing | escribe o ejecuta pruebas (unitarias, integración, specs) o revisa código de otro |
| Base de Datos | crea o cambia tablas, migraciones, índices o políticas de la base |
| DevOps | toca ramas, releases, CI, contenedores o despliegue |
| Documentación | escribe contratos, guías, wiki, diagramas u OpenAPI |
| Análisis | interpreta reglas de negocio o define criterios (por ejemplo la regla de riesgo) |
| Diseño / UX-UI | define la experiencia visual o de uso (pantallas, semáforo, diagramas de presentación) |
| Integración | conecta con otro equipo o servicio (contratos, eventos, clientes HTTP) |
| Configuración | cambia parámetros, propiedades o valores de entorno |
| Seguridad | afecta autenticación, autorización, RLS, privacidad o manejo de credenciales |
| Investigación | averigua algo que se desconoce (por ejemplo valores posibles de un contrato) |
| Gestión | coordina, cierra ramas, actualiza Taiga o hace seguimiento |
| Otro | no encaja en ninguna de las anteriores (en este sprint no hizo falta) |

Una tarea puede llevar más de una etiqueta. Las revisiones de código de otro integrante llevan **Testing**.

## Tareas por integrante y etiqueta

Cantidad de tareas que llevan cada etiqueta (una tarea con dos etiquetas cuenta en ambas).

| Integrante | Backend | Frontend | Testing | Base de Datos | DevOps | Documentación | Análisis | Diseño / UX-UI | Integración | Configuración | Seguridad | Investigación | Gestión | Otro | Tareas |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Luciano Paz | 2 | 5 | 3 | 2 | · | 2 | · | 2 | 4 | · | 2 | · | 2 | · | 12 |
| Mateo Carballo | 2 | 5 | 4 | 2 | 1 | 1 | 1 | 2 | 5 | 1 | 1 | · | 2 | · | 13 |
| Damián Baigorria | 4 | 2 | 1 | 2 | 1 | · | 1 | 1 | 3 | · | 1 | · | 2 | · | 9 |
| Joaquín Cortez | 4 | 1 | 1 | 2 | · | 1 | 1 | · | 2 | · | 2 | · | · | · | 7 |
| Valentina Maldonado | 3 | 5 | 2 | 1 | · | · | · | 2 | 4 | 1 | 1 | 1 | · | · | 10 |
| Máximo Cerquatti | 4 | 1 | 4 | 1 | · | 1 | · | · | 3 | 2 | 6 | · | · | · | 10 |
| Regina Cerasulo | 3 | 1 | 5 | 1 | · | · | 1 | · | 3 | · | 3 | · | · | · | 9 |
| Bruno Gianoli | 3 | 2 | 3 | 1 | 1 | 1 | 1 | · | 2 | · | 1 | · | 1 | · | 8 |
| Ana Ducart | · | · | · | · | · | 10 | · | 4 | 1 | · | 1 | · | 3 | · | 11 |
| **Total** | **25** | **22** | **23** | **12** | **3** | **16** | **5** | **11** | **27** | **4** | **18** | **1** | **10** | · | **89** |

## Índice maestro

| Dev | Integrante | ID | Tarea | Etiquetas | Líneas |
|---|---|---|---|---|---:|
| 01 | Luciano Paz | S2-00 | **Contratos compartidos del Sprint 2** (sin lógica): `ReportScopeResolver`, `TeacherMembershipPort`, `CohortSu | Backend, Frontend, Integración | 550 |
| 01 | Luciano Paz | 06B-T1 | Orden estricto del outbox por **clave de partición** en `findReadyToPublish` (`NOT EXISTS` de una `PENDING` má | Backend, Base de Datos | 65 |
| 01 | Luciano Paz | 05-N1 | Migrar las partes 01, 02, 04 y 06 a `/api/backoffice/...` (`admin-api-url.ts`) y retirar el parche de `proxy.c | Frontend, Integración | 350 |
| 01 | Luciano Paz | 04-T4 + 05-T4 | Suites WireMock del cliente T07: proveedores (200/404/409/503, key enmascarada) y activación (200/409/503) | Testing, Integración | 560 |
| 01 | Luciano Paz | #314 + #315 | Suite de integración de HU12 en PostgreSQL real: A→A 200, A→B 403, sin pertenencia 403, ADMIN 200, `ALL` solo  | Testing, Seguridad, Base de Datos | 600 |
| 01 | Luciano Paz | #313 | **Panel docente con semáforo** (FE): color **y** texto, teclado, WCAG AA, badge de frescura; contra `TeacherPa | Frontend, Diseño / UX-UI | 800 |
| 01 | Luciano Paz | #1657 | Estado de 2FA y sesión (FE) | Frontend, Seguridad | 300 |
| 01 | Luciano Paz | 14-T3 | Panel de umbrales + lista de alertas activas (FE de HU14) | Frontend, Diseño / UX-UI | 750 |
| 01 | Luciano Paz | H-05 | H-05: PR de `plan-mvp-sprint1-backoffice.md` y `auditoria-contratos-skillhub.md` (sin `Contexto.md`); rebase y | Documentación, Gestión | — |
| 01 | Luciano Paz | C6 | C6: seguimiento con T10 (vidas agotadas, `sandbox.events`) y fila de T10 en la tabla de firmas. Avisarle que P | Integración, Gestión | — |
| 01 | Luciano Paz | 06B-T6 | 06B-T6: documentar el orden por clave en el contrato del consumidor. Etiquetas: Documentación. | Documentación | — |
| 01 | Luciano Paz | Revisiones | Peer reviews asignadas | Testing | — |
| 02 | Mateo Carballo | PR #56 | **PAR-14, PAR-12 y alineación con Skill Hub: ya está hecha y abierta, queda asignada a vos** (ex 07-T1 y P-12  | Backend, Base de Datos, Configuración | 260 |
| 02 | Mateo Carballo | 01-IT | Test de integración de US-01 rescatado de `feature/us-01-testcontainers` (solo el test, sin `jsonKafkaTemplate | Testing, Base de Datos | 180 |
| 02 | Mateo Carballo | 05-T1 | Cliente real de modelos evaluadores (listar, activo, desplegar, activar, borrar) sobre la infraestructura de 0 | Backend, Integración | 600 |
| 02 | Mateo Carballo | #276 | Modal de conmutación con advertencia (actual → nuevo, confirmación explícita, UI en español) | Frontend, Diseño / UX-UI | 300 |
| 02 | Mateo Carballo | 05-T3 | Pantalla 10 conectada: quitar `MOCK_MODELS` de `llm-models.service.ts` | Frontend, Integración | 450 |
| 02 | Mateo Carballo | 05-N3 | Specs de guards y de la vista de solo lectura (ADMIN, GESTOR, PROFESSOR) | Testing, Frontend, Seguridad | 350 |
| 02 | Mateo Carballo | #322 | **Dashboard de KPIs** con "muestra insuficiente" y "disponible al cierre del curso", contra `CsatKpiDto` (pasó | Frontend, Diseño / UX-UI | 850 |
| 02 | Mateo Carballo | #3540 | Pantalla de corridas de calibración (parte 13) | Frontend, Integración | 750 |
| 02 | Mateo Carballo | 08-T3 | IT de consumidores T07/T05: nuevo, duplicado, malformado → DLT, flag apagado | Testing, Integración | 300 |
| 02 | Mateo Carballo | H-02 | H-02: cerrar sin mergear `feature/contratos-alineados-drive` y `feature/us-02-envelope`; en el FE, cerrar `fix | Gestión, DevOps | — |
| 02 | Mateo Carballo | C2 | C2: confirmar con T11 la materialización de `notifications.events` y el payload de `STUDENT_AT_HIGH_RISK`/`EXP | Integración, Gestión | — |
| 02 | Mateo Carballo | #307 | #307: documentar reglas de riesgo (incluidas R-1 y R-2) y esquema del read model. Etiquetas: Documentación, An | Documentación, Análisis | — |
| 02 | Mateo Carballo | Revisiones | Peer reviews asignadas | Testing | — |
| 03 | Damián Baigorria | #303 | Read model de cohorte: **`V19__reporting_cohort_read_model.sql`** (`cohort_roster`, `student_activity_summary` | Base de Datos, Backend | 420 |
| 03 | Damián Baigorria | #304 | Clasificador de riesgo **puro** (sin Spring) + recálculo. Umbrales en `reporting.risk.*`. Reglas decididas: ** | Backend, Análisis | 760 |
| 03 | Damián Baigorria | #312 | `STUDENT_AT_HIGH_RISK` por `DomainEventOutbox` **solo en la transición** a RED, misma transacción que el recál | Backend, Integración | 240 |
| 03 | Damián Baigorria | 15-T8 | **Builder de reportes de US-15** (FE): métricas, filtros, período, columnas, agrupación, "Guardar plantilla",  | Frontend, Diseño / UX-UI | 1.400 |
| 03 | Damián Baigorria | #3331 | Solo lectura de parámetros para PROFESSOR (FE); el backend ya lo permite (pasó de Bruno a vos) | Frontend, Seguridad | 200 |
| 03 | Damián Baigorria | 09-T1 | HU09: `V25__reporting_export_job.sql` + `POST /reports/exports` → job asíncrono → CSV → `EXPORT_READY` por out | Backend, Base de Datos, Integración | 1.000 |
| 03 | Damián Baigorria | H-06 | H-06: PR #54 `release/v1.0.0 → main` (CI verde, revisión, merge y tag `v1.0.0`) si todavía sigue abierta. Etiq | DevOps, Gestión | — |
| 03 | Damián Baigorria | C3 | C3: rehacer la solicitud a T02 (envelope de 6 campos, `courses.events`, distribución 1–5, abstenciones, dimens | Integración, Gestión | — |
| 03 | Damián Baigorria | Revisiones | Peer reviews asignadas | Testing | — |
| 04 | Joaquín Cortez | 15-T1 | Catálogo de métricas de US-15 (lista blanca por enum) con fuente y disponibilidad según el contrato; `GET /rep | Backend, Análisis | 550 |
| 04 | Joaquín Cortez | 15-T3 | Plantillas y favoritas, solo del dueño: **`V21__reporting_report_template.sql`** (`owner_id, course_id, config | Backend, Base de Datos | 850 |
| 04 | Joaquín Cortez | HU13 | **KPIs CSAT completos (pasó de Mateo a vos):** `V22__reporting_survey_summary.sql` (conteos por estrella, abst | Backend, Base de Datos, Seguridad | 1.600 |
| 04 | Joaquín Cortez | #3537 | Fachada del perfil de calibración institucional (golden set y rúbrica) sobre T07 | Backend, Integración | 480 |
| 04 | Joaquín Cortez | #3538 | Pantalla del perfil de calibración (parte 12) | Frontend, Integración | 750 |
| 04 | Joaquín Cortez | #324 | #324: políticas de privacidad y fórmulas de los KPIs. 06-T8: OpenAPI de la fachada de calibración (en el códig | Documentación, Seguridad | — |
| 04 | Joaquín Cortez | Revisiones | Peer reviews asignadas | Testing | — |
| 05 | Valentina Maldonado | #291 | **Badge de frescura: ya está re-aplicado con los fixes de la review** en `feature/mvp-s7-ingestion-ui` (`d175c | Frontend, Diseño / UX-UI | 530 |
| 05 | Valentina Maldonado | #305 | Proyector desde `reporting.ingested_event` hacia el read model de V19 (T03 `CHALLENGE_COMPLETED`, T02 `ROSTER_ | Backend, Base de Datos, Integración | 1.250 |
| 05 | Valentina Maldonado | 10-M1 | `DataFreshnessProvider` → `DataFreshnessDto {asOf, stale, thresholdMinutes, sources[]}` calculado **al leer**  | Backend | 480 |
| 05 | Valentina Maldonado | 05-N2 | Dashboard: ocultar accesos no permitidos a GESTOR y PROFESSOR | Frontend, Seguridad | 220 |
| 05 | Valentina Maldonado | 05-T7 | Specs de las pantallas 09 y 10 conectadas | Testing, Frontend | 450 |
| 05 | Valentina Maldonado | 08-T1 | Consumidores de `llm.events` (T07, filtrando por `eventType`) y de T05, con flag, dedup y DLT (mismo patrón qu | Backend, Integración, Configuración | 600 |
| 05 | Valentina Maldonado | #284 | Indicador de veredicto y deriva, y banner de conmutación (**en Taiga figura Joaquín: pedirle a Ana que lo reas | Frontend, Diseño / UX-UI | 300 |
| 05 | Valentina Maldonado | 09-T2 | Conectar las export tools de la slice 07 al export asíncrono de HU09 | Frontend, Integración | 250 |
| 05 | Valentina Maldonado | C8 | C8: confirmar con T03 los valores posibles de `resultado` (o `result.status`) y completar la fila de T03 en la | Integración, Investigación | — |
| 05 | Valentina Maldonado | Revisiones | Peer reviews asignadas | Testing | — |
| 06 | Máximo Cerquatti | #3512 | **La rama ya está pusheada** (`feature/tema-12-admin-route-guards`, `8c2c82e`): sincronizar con `develop`, abr | Frontend, Seguridad | 98 |
| 06 | Máximo Cerquatti | 04-T1 | Infraestructura del cliente HTTP a T07 (`RestClient` administrado, auth según C1, `problem+json` → excepciones | Backend, Integración, Seguridad | 750 |
| 06 | Máximo Cerquatti | #311 | **Capa de acceso de reporting:** `ReportScopeResolver` + `TeacherMembershipPort` (adaptador T02 por Gateway co | Backend, Base de Datos, Seguridad | 1.250 |
| 06 | Máximo Cerquatti | 06B-T4 | Pasar el topic de auditoría (`TOPIC_AUDIT_EVENTS`, `DEFAULT_AUDIT_TOPIC`) de constante a propiedad tipada (el  | Backend, Configuración | 100 |
| 06 | Máximo Cerquatti | 15-T5 | Tests del motor de US-15: RLS, anti-comparación, anonimato, lista blanca (métrica desconocida → 400) | Testing, Seguridad | 700 |
| 06 | Máximo Cerquatti | 06B-T3 | Verificar `GATEWAY_SHARED_SECRET` con el mecanismo que acuerde T01 sin romper el entorno local | Backend, Seguridad, Configuración | 160 |
| 06 | Máximo Cerquatti | 06-T5 + 07-T4 | Tests WireMock de calibración y del estado de calibración | Testing, Integración | 580 |
| 06 | Máximo Cerquatti | 14-T4 | Tests de alertas (HU14): en el límite, 1 punto abajo, no-ADMIN 403 | Testing | 250 |
| 06 | Máximo Cerquatti | C1 | C1: dueño del contrato con T07 y de la fila de T01: confirmar `/api/llm/admin/` (ruta, Gateway o Eureka, token | Integración, Seguridad, Documentación | — |
| 06 | Máximo Cerquatti | Revisiones | Peer reviews asignadas | Testing | — |
| 07 | Regina Cerasulo | 04-T2 | Cliente real de proveedores y credenciales (providers, provider-credentials, discover-models, test-model) sobr | Backend, Integración, Seguridad | 660 |
| 07 | Regina Cerasulo | 04-T3 | Pantalla 09 conectada a `/api/backoffice/llm/...` | Frontend, Integración | 350 |
| 07 | Regina Cerasulo | #310 | `GET /api/backoffice/reports/courses/{courseId}/teacher`: alumnos con semáforo y factores, promedio **solo del | Backend, Seguridad | 700 |
| 07 | Regina Cerasulo | #306 | 12 casos de riesgo: límites (10/11 y 4/5 días, 39/40, 60/61 y 69/70 %) más R-1, R-2 y los 3 escenarios de Taig | Testing, Análisis | 300 |
| 07 | Regina Cerasulo | 10-M2 | Tests del proveedor de frescura (14/15/16 min, fuente sin eventos, PAR-23 modificado) **y del proyector #305** | Testing | 400 |
| 07 | Regina Cerasulo | #323 | **Tests de anonimato de HU13** (pasó de Joaquín a vos): 4, 5 y 6 respuestas con PAR-18 = 5; curso abierto → si | Testing, Seguridad | 300 |
| 07 | Regina Cerasulo | 06B-T2 | **IT del orden del outbox** (pasó de Máximo a vos): falla v1 y v2 no sale antes (Testcontainers con Kafka), so | Testing, Integración | 280 |
| 07 | Regina Cerasulo | HU14 | Alertas configurables (BE): `V23__reporting_alerts.sql` (`alert_threshold` + `alert`, con RLS si hay `course_i | Backend, Base de Datos | 1.000 |
| 07 | Regina Cerasulo | Revisiones | Peer reviews asignadas | Testing | — |
| 08 | Bruno Gianoli | 15-T2 + 15-T4 | **Ruta crítica de US-15:** motor de consultas dinámicas (filtros, período, columnas, agrupación) **y** `POST / | Backend, Seguridad, Base de Datos | 2.100 |
| 08 | Bruno Gianoli | 15-T9 | **Specs del builder de US-15** (FE, de Damián): crear, editar y correr plantilla, validaciones, 403 | Testing, Frontend | 450 |
| 08 | Bruno Gianoli | 12-T9 | **Specs del panel docente** (FE, de Luciano) | Testing, Frontend | 250 |
| 08 | Bruno Gianoli | #3539 | Fachada de corridas de calibración (crear, listar, detalle; `maeFinal`, `maxIndividualError` y veredicto **de  | Backend, Integración | 500 |
| 08 | Bruno Gianoli | 07-T2 | Estado de calibración del modelo activo (último veredicto y deriva, leídos de T07) | Backend, Integración | 460 |
| 08 | Bruno Gianoli | H-04 | H-04: `feature/mvp-s6-golden-set-runs` no se mergea (MAE y veredicto son de T07 por la Opción A; la PR #30 ya  | Gestión, DevOps | — |
| 08 | Bruno Gianoli | #286 | #286: documentar que T07 calcula MAE y veredicto, el Backoffice gobierna PAR-14 y la deriva la emite T07 (gate | Documentación, Análisis | — |
| 08 | Bruno Gianoli | Revisiones | Peer reviews asignadas | Testing | — |
| 09 | Ana Ducart | D4 | **Taiga coherente desde el CP0** (`auditoria-sprint1.md` §5): corregir estados incoherentes (#18, #1628 y #162 | Gestión, Documentación | — |
| 09 | Ana Ducart | T-C | **Nueva: seguimiento de contratos.** Una tarjeta por tema fuente (T02, T03, T05, T07, T08, T10 y T11) con resp | Gestión, Integración | — |
| 09 | Ana Ducart | W-1 | **Nueva: páginas de wiki del Sprint 1** con la plantilla de la cátedra: **"G06 - Parámetros globales y adminis | Documentación, Diseño / UX-UI | — |
| 09 | Ana Ducart | D1 | Secuencia del cambio de parámetro (diferido del S1): va en la página de parámetros | Documentación, Diseño / UX-UI | — |
| 09 | Ana Ducart | D5 | **Nuevo:** diagrama de microservicios "fuentes de datos → reportes" (la lámina 6 del PDF de arquitectura aplic | Documentación, Diseño / UX-UI | — |
| 09 | Ana Ducart | #316 | Página **"G06 - Reportes y panel docente"**: endpoints del panel, política RLS (explicada) y aviso `STUDENT_AT | Documentación, Seguridad | — |
| 09 | Ana Ducart | 15-T10 | En la misma página: vista del builder de US-15 y catálogo de métricas (qué mide cada una y de qué tema sale) | Documentación | — |
| 09 | Ana Ducart | D-DEMO | Guion de la demo del S2 + checklist E2E (panel docente, reportes dinámicos y fachada LLM); lo revisa Luciano | Documentación, Gestión | — |
| 09 | Ana Ducart | 06-T6 | Secuencia ADMIN → Backoffice → T07 (calibración) en la página de gobernanza LLM | Documentación, Diseño / UX-UI | — |
| 09 | Ana Ducart | 14-T5 | Sección de alertas configurables (catálogo de umbrales, sin evento `THRESHOLD_BREACHED`) | Documentación | — |
| 09 | Ana Ducart | 09-T4 | Sección de exportación asíncrona y aviso `EXPORT_READY` | Documentación | — |
