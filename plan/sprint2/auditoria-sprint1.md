# Auditoría del Sprint 1 — Backoffice (Tema 12)

> **Fecha de corte:** 29/09/2026 · `develop` BE en `6f6a26a` (incluye PRs #47, #48 y #50 mergeadas hoy) · `develop` FE sincronizado el mismo día · Taiga proyecto 1804026, sprint "G06 - Sprint 1" (solo lectura).
> **Método:** cada rama se comparó contra `develop` **simulando el merge** (`git merge-tree`), no con `git diff develop...rama`, para no confundir archivos que ya entraron por otra PR con trabajo pendiente.

---

## 1 · Resumen ejecutivo

1. **Se hizo mucho más de lo que Taiga refleja:** 144 de 155 tareas cerradas (93 %), pero solo 6 de 13 historias cerradas (46 %) y 19 de 34 SP (56 %). El cuello de botella **no fue programar**: fue **cerrar historias** (contratos con otros equipos, ramas sin PR, tareas reabiertas).
2. **Hay trabajo terminado que nunca llegó a `develop`:** guards (#3512), badge de frescura (#291) y un test de integración de US-01 están en ramas sin PR. Otras ~3.500 líneas quedaron fuera porque se pisaron con otro trabajo o con una decisión de alcance (Opción A).
3. **Las 4 áreas del Sprint 1 están implementadas en su núcleo**, pero ninguna está cerrada de punta a punta: la gestión LLM funciona contra **stubs**; el registro de parámetros tiene un hueco con **PAR-12**; los contratos de lectura están sin firmar con **T02** y **T05**.
4. **Los contratos de lectura son la ruta crítica del Sprint 2** (lámina 6 del PDF de arquitectura): sin T02 no hay pertenencia docente, ni padrón, ni CSAT; sin eso el panel docente no se puede cerrar.
5. **Taiga tiene 6 historias con estado incoherente** y 11 tareas del Sprint 1 todavía abiertas, y **G06 no tiene ninguna página en la wiki** que pide la cátedra.

---

## 2 · Estado por área del alcance del Sprint 1

### 2.1 · Administración de plataforma

| Qué | Evidencia en `develop` | Estado |
|---|---|---|
| Borde de seguridad con headers del Gateway v3 y autorización por rol | `GatewayAuthenticationFilter`, `SecurityExpressions`, `ErrorApiSecurityHandler` (PRs #27, #34, #37) | ✅ |
| Auditoría administrativa delegada en T01 + publicación en `identity.audit.events` por outbox | `AdministrativeAuditController` (`GET /audit`, ADMIN), `AdministrativeAuditPublisherImpl` (PRs #37, #38, #47) | ✅ |
| Cuentas ADMIN (alta/baja de rol) | Delegado en T01; pantalla 03 del FE llama a `/api/users/**` (contrato confirmado en la PR #50) | ✅ |
| Guards de rutas del FE | Rama `feature/tema-12-admin-route-guards` (`8c2c82e`, 98 líneas), **sin PR** | ⚠️ tarea #3512 abierta |
| Estado de 2FA y sesión | Depende de T01 | ⚠️ #1657 abierta |
| Secreto compartido del Gateway | `GatewayTrustProperties` existe; la variable `GATEWAY_SHARED_SECRET` queda vacía por defecto | ⚠️ En una sesión anterior se observó que la instancia del mesh acepta headers de rol si se la llama sin pasar por el Gateway. **Re-verificar en 06B-T3.** |

### 2.2 · Registro de parámetros PAR-01 a PAR-24

| Qué | Evidencia | Estado |
|---|---|---|
| Registro unificado con estado (CONFIRMED/CANDIDATE), tipo y consumidores | `V8__global_parameter_registry_metadata.sql` (PR #39) | ✅ |
| Versionado, bloqueo optimista, historial inmutable, `Idempotency-Key` | V4, V7, `ParameterChangeRecorderImpl`, `IdempotencyKeyGuard` (PRs #29, #39) | ✅ |
| Propagación por outbox (`GLOBAL_CONFIGURATION_CHANGED`, clave = `param_key`) | `DomainEventOutboxImpl`, publisher con reintentos y DLT | ✅ |
| Lectura M2M con scope `backoffice.parameters.read` | PRs #51/#52, desplegado | ✅ |
| Exclusión de externos (PAR-03/06/07 → T09, PAR-24 → T01) | V2 los excluye | ✅ |
| **PAR-12** (vidas iniciales/máximas) | `PARAMETROS.md` lo declara **CONFIRMADO del Backoffice** (lo consume T10); `AGENTS.md` dice **EXTERNO T08**; V2 lo excluye y ningún seed lo agrega | ❌ **contradicción documental + hueco en el registro** → P-12 |
| **PAR-14** (tolerancia de calibración) | `ParameterValueRules` solo valida "es un mapa"; el PRD exige ±5 promedio y ±10 individual | ⚠️ → 07-T1 |
| PAR-21 | Suspendido y documentado (no está en el PRD) | ✅ correcto no sembrarlo |
| Orden estricto por clave en el outbox (US-02 CA4) | La PR #46 demostró que `findReadyToPublish` puede publicar v2 antes que v1 con reintentos | ⚠️ → 06B-T1/T2 |
| IT con Testcontainers del servicio de parámetros (US-01 T8) | Rama `feature/us-01-testcontainers` (177 líneas de test), **nunca tuvo PR** | ⚠️ → H-01 |
| Solo lectura para PROFESSOR en el FE | Backend OK (`PARAMETER_READERS`); FE pendiente | ⚠️ #3331 abierta |

### 2.3 · Gestión del proveedor LLM, exclusiva de ADMIN

| Qué | Evidencia | Estado |
|---|---|---|
| Endpoints de proveedores, credenciales, descubrimiento, test, modelos, activo, desplegar, activar, borrar | `LlmProviderController`, `LlmModelController`: **todos con `@PreAuthorize(ADMIN)`** | ✅ exclusivo de ADMIN |
| Cliente hacia T07 | `DefaultLlmProviderClient` y `DefaultLlmAdminClient` son **stubs**: devuelven listas vacías o 503 "not configured" | ❌ no funciona de punta a punta → 04-T1, 04-T2, 05-T1 |
| Pantalla 10 (modelos) | `llm-models.service.ts` sirve `MOCK_MODELS` | ❌ → 05-T3 |
| Modal de conmutación | #276 abierta | ⚠️ |
| Golden set y corridas (HU06/HU07) | La rama `feature/mvp-s6-golden-set-runs` (3.142 líneas) implementaba MAE y veredicto **locales**; la PR #30 se cerró porque, con la Opción A, eso es de T07 | ➖ trabajo descartado · fachada pendiente con gate C1 |

### 2.4 · Contratos de lectura con los seis temas que proveen datos

| Qué | Evidencia | Estado |
|---|---|---|
| Registro de contratos (tabla + endpoint + pantalla 14) | V14, `ReadContractController` (`GET /reports/contracts`, ADMIN) (PR #45) | ✅ |
| Consumidores con dedup y DLT | `ChallengeEventConsumer`, `CourseEventConsumer` activos; `EconomyTransactionConsumer`, `SandboxEventConsumer` con flag apagado; `processed_event`, `KafkaDeadLetterPublisher` | ✅ |
| Frescura PAR-23 de la ingesta | `IngestionReportController` (`/health/freshness`), cálculo al leer | ✅ |
| **Seed de V14 con topics viejos** | `courses.lifecycle`, `challenges.results`, `economy.transactions` · desde la PR #47 los reales son `courses.events`, `challenges.events`, `accounting.events` | ❌ la pantalla 14 muestra datos desactualizados → V21 |
| Estado de los contratos | T01 ✅ · T03 🟡 ACUERDO · T08 🟡 ACUERDO parcial · T10 🟡 EN CURSO · **T02 🟡 SOLICITUD LISTA sin respuesta** · **T11 sin firma** · **T05 ⏳ sin solicitud** | ❌ → HT01 |
| Tabla de firmas (G4) | Todas las filas con `⛔ a completar` | ❌ → cada responsable completa su fila (C1, C2, C3, C5, C6, C8, C9); Ana hace el seguimiento en Taiga (T-C) |
| Solicitud a T02 | Escrita con el **envelope viejo de 8 campos** y `course.events`; pide CSAT como **promedio** | ❌ → C3 |

---

## 3 · Cumplimiento del PRD (lo que toca al Backoffice)

| Requisito | Qué exige | En `develop` | Brecha | Tarea S2 |
|---|---|---|---|---|
| RF-CFG-04 | Catálogo de la economía como configuración global solo ADMIN | ✅ | PAR-12 | P-12 |
| RF-CFG-05 | Ámbitos ADMIN (valor) vs PROFESOR (estructura del curso) | ✅ backend | FE de solo lectura | #3331 |
| RF-CFG-06 | Los cambios rigen hacia adelante | ✅ versionado + historial | Orden estricto por clave | 06B-T1 |
| PAR-14 (RF-IA-31) | ±5 promedio y ±10 en una dimensión | ⚠️ | Validación de rango | 07-T1 |
| PAR-18 (RF-ENC-13) | Mínimo 5 respuestas para mostrar encuestas al PROFESOR | ✅ sembrado | No se aplica en ningún reporte | HU13 |
| RF-ENC-04 | Anonimato estructural: ni autor ni timestamp que permita correlacionar | — | **La ingesta cruda guardaría el `occurred_at` exacto de cada respuesta** si T02 publica por respuesta | C3 + HU13 |
| RF-ENC-08 | KPIs agregados: PROFESOR los suyos, ADMIN consolidado + desglose por curso | ❌ | Todo | HU13 |
| RF-ENC-10 | Abstenciones fuera del denominador pero informadas | ❌ | **La propuesta no lo tenía** | HU13 |
| RF-ENC-13 | Resultados al PROFESOR solo **al cierre del curso** y sobre PAR-18 | ❌ | **La propuesta solo tenía el umbral** | HU13 |
| KPI-01/02 | % 4–5 y % 1–2 sobre respuestas (metas 80 % y 10 %) | ❌ | Requiere **distribución**, no promedio | C3 + HU13 |
| Tabla 10 (Analítica) | Dashboards PROFESOR/ADMIN de engagement, progreso y **uso de IA** | ❌ | Uso de IA se puede calcular con T03 (`usoTutorIa`) | US-15 (catálogo) |
| RF-IA-30/31/32 | Calibración del evaluador dentro de PAR-14 | ➖ de T07 (Opción A) | Fachada | HU06/HU07 (gate C1) |
| RF-ROL-01..06 | Gestión de ADMIN | ✅ vía T01 | — | — |

---

## 4 · Ramas fuera de `develop`

### 4.1 · Backend (`2026-P4-BE/tpi-backoffice`)

| Rama | Autor | Qué tiene realmente (merge simulado) | Conflictos | Veredicto | Dueño de la acción |
|---|---|---|---|---|---|
| `feature/mvp-s6-golden-set-runs` | Bruno | 43 archivos, 3.142 líneas: entidades, MAE y veredicto locales, stubs de ciclo de vida | 7 (`DomainEventOutbox`, `BackofficeException` y familia, `SecurityExpressions`: ya existen en `develop` con otra forma) | **No mergear** (contradice la Opción A). Tag de archivo y borrar | Bruno (H-04) |
| `feature/us-01-testcontainers` | Mateo | `GlobalParameterServiceIntegrationTest` (177 líneas) + un `jsonKafkaTemplate` en `KafkaProducerConfig` + 1 línea de propiedades | 0 | **Rescatar solo el test** (la auditoría ya no usa ese template) | Mateo (H-01) |
| `feature/us-02-envelope` | Mateo | `EventEnvelope`, `EventEnvelopeFactory` y payloads en `administration` | 0 | **Cerrar**: duplica `reporting/dtos/events/EventEnvelope` y el armado de `DomainEventOutboxImpl` (S3) | Mateo (H-02) |
| `feature/contratos-alineados-drive` | Mateo | `CONTRATOS_JSON.md` con el estándar del Drive (PR #6 cerrada) | 1 | **Cerrar**: superada por el mapeo ratificado con T11 (v3) | Mateo (H-02) |
| `feature/contexto-sprint1` | Luciano | Plan MVP v2 del S1 (676 líneas), auditoría Skill Hub, `Contexto.md`, propuesta FE | 1 (add/add en la propuesta FE) | **PR solo del plan y la auditoría** (trazabilidad del S1); `Contexto.md` no va al repo | Luciano (H-05) |
| `fix/development-doc-real-workflow` | Luciano | `DEVELOPMENT.md` (flujo real, `mvn clean package` antes del Docker) + regla de inglés en `AGENTS.md` | 1 (`AGENTS.md`: la regla ya entró por la #28) | **Rebase y PR solo de `DEVELOPMENT.md`** | Luciano (H-05) |
| `feature/us-03-gateway-auth` (remota) | — | Nada nuevo (ya entró) | — | Borrar | Quien la creó |
| `feature/us-03-gateway-auth` (**copia local** de Luciano, `5286230`) | commit de Regina | Vacía `verify.yml` (ya resuelto: el CI corre solo en `main`) | 1 | Descartar la copia local | Luciano |

### 4.2 · Frontend (`2026-P4-FE/2026-PIV-TPI-FE`, solo ramas del Tema 12)

| Rama | Autor | Qué tiene | Veredicto | Dueño |
|---|---|---|---|---|
| `feature/tema-12-admin-route-guards` | Máximo | Guards ADMIN con `permissionGuard` + spec (98 líneas), sin conflictos | **Abrir PR** (#3512) | Máximo |
| `feature/mvp-s7-ingestion-ui` | Valentina | Badge de frescura **re-aplicado con los fixes de la review** (`d175ca1`, 533 líneas), sin conflictos | **Abrir PR** (#291): no hay que rehacerlo | Valentina |
| `fix/admin-export-service-spec` y 3 commits de `feature/tema-12-backoffice` | Mateo | Fix del spec del export (PR #121) | **Cerrar**: `develop` ya tiene una versión más estricta del mismo autor (`3899e9d` + `495e29b`); el merge daría conflicto sin aportar nada | Mateo (H-02) |

### 4.3 · Release

La PR #54 `release/v1.0.0 → main` (Damián) está **abierta**, mergeable, con revisión requerida y CI en curso. Es el cierre formal del Sprint 1 → H-06.

---

## 5 · Taiga (Sprint 1)

### 5.1 · Números

| Métrica | Valor |
|---|---|
| Historias | 13 · cerradas 6 (46 %) |
| Story points | 34 · completados 19 (56 %) |
| Tareas | 155 · cerradas 144 (93 %) |

### 5.2 · Estados incoherentes (corregir en el CP0)

| Historia | Estado mostrado | ¿Cerrada? | Realidad | Acción |
|---|---|---|---|---|
| #18 HU04 | New | Sí | Clientes stub, falta el real | Reabrir y mover al S2 |
| #1629 HT01 | New | Sí | Contratos sin firmar (T02, T05, T11) | Reabrir y mover al S2 |
| #1628 HU08 | In progress | Sí | Faltan consumidores T07/T05 | Reabrir como HU08-bis |
| #17 HU01 | Done | No | #3331 abierta | Cerrar al terminar #3331 |
| #28 HU10 | Done | No | #291 abierta | Cerrar al mergear #291 |
| #182 HU03 | Done | No | #3512 abierta | Cerrar al mergear #3512 |

### 5.3 · Tareas del Sprint 1 todavía abiertas (11)

| Ref | Tarea | Asignada en Taiga | En el plan del S2 |
|---|---|---|---|
| #276 | Modal de conmutación | Mateo | Mateo |
| #284 | Indicador de deriva y banner | **Joaquín** | **Valentina** (la propuesta la reasignó: falta actualizar Taiga) |
| #286 | Documentar evaluación, PAR-14 y deriva | Bruno | Bruno (gate C1) |
| #291 | Badge de frescura | Valentina | Valentina (solo PR) |
| #1657 | 2FA y sesión | Regina | Regina (gate T01) |
| #3331 | Parámetros de solo lectura para PROFESOR | Bruno | Bruno |
| #3512 | Guards de rutas | Máximo | Máximo (solo PR) |
| #3537 | Casos del golden set → **fachada del perfil** | Joaquín | Joaquín (gate C1) |
| #3538 | Pantalla de casos → **pantalla del perfil** | Joaquín | Joaquín (gate C1) |
| #3539 | Corridas y MAE → **fachada de corridas** (el MAE lo calcula T07) | Bruno | Bruno (gate C1) |
| #3540 | Pantalla de corridas | Bruno | Bruno (gate C1) |

> Las #3537 y #3539 tienen títulos que ya no corresponden (hablan de implementar casos y calcular MAE en el Backoffice). Renombrarlas en Taiga para que nadie las implemente como en la rama S6.

### 5.4 · Wiki de Taiga: G06 no tiene página

La cátedra pide documentar cada tema en la wiki (`guia-doc-proyecto-por-grupo`, plantilla `template-proyecto-por-grupo`): páginas **"GXX - TEMA"** con descripción, historias enlazadas, diagramas en Draw.io en orden DER → BPMN → Clases → Estados → Secuencias → Microservicios, y endpoints con ejemplos.

| Grupos con página en la wiki (29/09) | Grupos sin página |
|---|---|
| G02, G03, G05, G07, G08, G10, G11, G12 | G01 (solo tiene un spike), G04, **G06**, G09 |

→ Entregable principal de Ana en el Sprint 2 (W-1, #316, 15-T10, D1, D5), con insumos y revisión de cada dueño de historia.

---

## 6 · Pull requests del backend

| Métrica | Valor |
|---|---|
| PRs totales | 54 |
| Mergeadas | 44 |
| Cerradas sin merge | 9 |
| Abiertas | 1 (#54 release) |

**Por qué se cerraron sin merge (aprendizaje, no culpa):**

| PR | Motivo |
|---|---|
| #7, #26, #49 | Prefijo de rama `docs/` rechazado por el chequeo de nombres; se reabrieron como `feature/` |
| #3, #4 | Reemplazadas por nuevas PRs de la misma rama (#11, #12) |
| #5 | Apuntaba a `main` por error |
| #53 | `develop → main` directo (no permitido: solo por `release/*`) |
| #6 | Superada por el mapeo de topics ratificado con T11 |
| #30 | S6 local descartado por la Opción A |

---

## 7 · Otros hallazgos técnicos (para las reviews del S2)

| # | Hallazgo | Dónde | Recomendación |
|---|---|---|---|
| T-1 | El topic de auditoría es **constante** en dos clases | `ParameterAuditPublisherImpl.TOPIC_AUDIT_EVENTS`, `AdministrativeAuditPublisherImpl.DEFAULT_AUDIT_TOPIC` | 06B-T4: propiedad tipada |
| T-2 | `IngestionReportController` usa `"hasRole('ADMIN')"` literal en lugar de `SecurityExpressions.ADMIN` | `reporting/controllers` | Unificar cuando alguien toque el archivo (no abrir tarea sola) |
| T-3 | El FE todavía usa `/api/administration` y `/api/reports` | `08-resilience-error-api/data-access/admin-api-url.ts` | 05-N1 (sin borrar los alias del BE en el S2) |
| T-4 | La frescura se calcula al leer; no hay (ni hace falta) un marcador programado | `IngestionCounter`, `SourceIngestionStatsDto` | Reusar ese diseño en los reportes (10-M1 redefinida) |
| T-5 | `THRESHOLD_BREACHED` y `DATA_STALE_DETECTED` están **excluidos** del catálogo de T11 | `CONTRATOS_MAPEO_TOPICS.md` | HU14 con alertas internas |
| T-6 | `llm.budget.events` no está registrado en T11 | Ídem | B-AL al backlog |
| T-7 | Las migraciones V9–V13 y V16 quedaron reservadas y sin usar | `db/migration` | No reutilizarlas: el S2 arranca en V18 |
