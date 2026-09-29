# Plan MVP · Sprint 1 · Backoffice (Tema 12 · G06) — v2 (rebalanceada)

> **⚠️ SUPERSEDIDO en la parte LLM (S4, S5, S6 y la parte de casos de S8):** el 26/09 la auditoría `docs/Task/auditoria-contratos-skillhub.md` (rama `feature/contexto-sprint1`) **reemplazó el pilar "Gestión del proveedor LLM"** por la **opción A**: el dominio LLM (proveedores, credenciales, modelos, activación, golden set, calibración PAR-14) es de **T07** (`llm-service` v2.0.0) y el Backoffice es **fachada de gobernanza** que consume `/api/llm/admin/*` — **sin tablas ni lógica LLM propias**. Las secciones §5.2 (V10/interfaces LLM) y §6 (slices S4/S5/S6, S8-casos) de este documento quedan obsoletas; el resto del plan sigue vigente. También aplican: rutas bajo `/api/backoffice/**` (`${app.api.private-path}`) y roles del Gateway `ADMIN/GESTOR/PROFESSOR/STUDENT/MS`.

> **✅ ENTREGA DEL SPRINT 1 (verificada en los repos oficiales, 28/09):** backend `develop` `944992f` (US-01 parámetros, US-02 outbox/Kafka/DLT, US-03 seguridad/cuentas vía T01, US-08 ingesta acotada + S8 contratos, US-10 frescura parcial, contrato T11/T01/T07) y frontend `develop` `cfaac18` (backoffice administrativo completo, slices 01–14, shell, ErrorApi, proxy). **Pendiente de merge (cierre S1):** back PRs #47 (topics T11 v3), #48 (placeholder), #50 (contrato T01) y el M2M real a T07. **Lo LLM (US-04/05/06/07) pasa al Sprint 2** como fachada a T07 — ver `plan/sprint2/tareas-sprint2.md`.

> **Estado:** PROPUESTA para aprobar en la daily · **Autor:** Luciano Paz (Dev 1) · **Fecha:** 25/09/2026
> **Cierre del sprint:** domingo **27/09/2026** (milestone `G06 - Sprint 1`, Taiga ID 531925, fin 27/09)
> **Relevado en modo lectura:** `develop` y las ramas sin mergear de `2026-P4-BE/tpi-backoffice` · repo FE `2026-P4-FE/2026-PIV-TPI-FE` · Taiga (5 épicas G06 + 15 historias) · contratos de `feature/documentacion-contratos-parametros` · `Contexto.md` · `docs/Task/tareas-frontend-sprint1-propuesta.md`.
> **Qué cambió respecto de v1:** el reparto se rehízo con tres criterios: **(1) cada dev hace backend + frontend + tests de su propia funcionalidad**, **(2) líneas efectivas parejas** (código + tests; la documentación NO cuenta) y **(3) mínima dependencia entre compañeros**. La documentación pasa a Ana (MSII).
> **Trabajo con IA:** no hay estimaciones en horas. Hay **checkpoints con hora de corte** (§8). Si un slice no llega a su checkpoint, se corta por prioridad (P0 → P1 → P2), no se estira el plazo.

---

## 0. Resumen en 60 segundos

**Qué mostramos el 27/09.** Un ADMIN entra por el front, a través del Gateway de T01:

1. **Administración de plataforma:** borde de seguridad real (headers del Gateway), roles ADMIN/PROFESOR/MS, errores `ErrorApi`, auditoría y gestión de administradores (T01).
2. **Registro de parámetros PAR-01..PAR-24:** catálogo completo (propios + candidatos + externos como referencia), edición versionada con historial, idempotencia y **propagación por Outbox → Kafka** a los consumidores (T03, T07, T10…).
3. **Gestión del proveedor LLM (exclusiva de ADMIN):** alta de proveedores con clave cifrada y enmascarada, catálogo de modelos, revisión contra el **golden set**, **aprobación por tolerancia PAR-14**, activación con evento `MODEL_PROVIDER_CHANGED` hacia T07 y **fallback por deriva**.
4. **Contratos de lectura con los seis temas:** registro del estado de cada contrato (T02, T04, T05, T07, T08 y T10, más T03), consumidores Kafka con deduplicación y DLT, y **control de frescura** (15 min, PAR-23).

**Cómo:** 1 tren de merges (S0) + **1 PR de "contratos compartidos"** (viernes 12:00) + **8 slices verticales parejos, uno por dev: cada uno con backend, frontend y tests propios** (~1.900–2.250 líneas efectivas cada uno, §4.6). Ana Paula se ocupa de toda la documentación, Taiga y los diagramas.

**Las 6 reglas de oro:**

1. **Nadie arranca su slice hasta que S0 deje `develop` consolidado** y esté mergeado el PR de contratos compartidos (§5.2).
2. **Cada uno toca solo sus paquetes y carpetas** (§4.1). Los archivos compartidos tienen un único dueño (§4.3).
3. **Nadie usa la clase de otro compañero, solo su interfaz congelada** (§5.2). En los tests unitarios esa interfaz se mockea. Así nadie espera a nadie.
4. **Cada slice usa solo sus versiones de Flyway reservadas** (§4.2).
5. **Todo PR corre `mvn clean verify` en local antes de abrirse** y lo revisa otra persona (§9). Por decisión del equipo, `verify.yml` corre **solo hacia `main`/`release/**`** para no gastar minutos de GitHub Actions: el gate de `develop` es **local**.
6. **La demo (§3) es el criterio de aceptación global.** Lo que no aparece en la demo no es P0.

---

## 1. Estado real verificado (25/09)

### 1.1 Lo que ya funciona en `develop`

| Pieza | Evidencia |
|---|---|
| `GET/PUT /api/administration/parameters[/{key}]` con versionado, `Idempotency-Key` y validación | `ParameterController`, `GlobalParameterServiceImpl`, `IdempotencyKeyGuard`, `V4__idempotency_key.sql` |
| Seed PAR-01..18 sin externos (14 parámetros) | `V2__global_parameter.sql` |
| Outbox transaccional → `administration.events` (key = `param_key`), reintentos y DLT | `OutboxPublisherServiceImpl`, `V1`, `V3`, PR #16 |
| Envelope de 6 campos, `producer = tema-12-backoffice-service` | `ParameterChangeRecorderImpl` |
| Auditoría del cambio a `identity.audit` | `ParameterAuditPublisherImpl` (PR #11) |
| `ErrorApi{timestamp,status,error,message}` + `GlobalExceptionHandler` | PR #12 |
| **Seguridad real:** filtro del Gateway, `@EnableMethodSecurity`, `anyRequest().authenticated()`, `IdentityAuditClient` hacia T01 | **PR #27** (25/09) |
| Tabla de deduplicación `reporting.processed_event` | `V5__processed_event.sql` (PR #9) |
| Mapeo oficial de topics ratificado por T11 | `docs/Contracts/CONTRATOS_MAPEO_TOPICS.md` (PR #21) |

### 1.2 Terminado pero SIN mergear

El tren completo se simuló con `git merge-tree` en el orden de §5.1. **Solo hay 1 conflicto real.**

| Rama | Autor | Qué trae | Estado | Resultado de la simulación |
|---|---|---|---|---|
| `feature/infra-platform-integration` | Luciano | OTel, Vault `config.import`, Eureka/Tailscale | 🟡 PR #24, falta 1 approve | ✅ Limpio |
| `feature/us-02-kafka-headers` | Bruno | Columnas `request_id` y `traceparent` (**`V6`**); el publisher las reenvía como headers | 🟡 PR #22 **draft** | ✅ Limpio, pero **nadie completa esas columnas** (H13) |
| `feature/us-01-parametros` | Damian | Bloqueo optimista, historial inmutable **`V7`**, excepciones de dominio | 🟡 PR #29, falta 1 approve | ✅ Limpio |
| `feature/us-01-testcontainers` | Mateo | Tests de integración US-01 T8 | ⏳ Sin PR | ❌ **Conflicto** en `GlobalParameter.java` y `GlobalParameterServiceImpl.java` |
| `feature/us-02-envelope` | Mateo | `EventEnvelope<T>` + `EventEnvelopeFactory` | ⏳ Sin PR | ✅ Limpio |
| `feature/us-08-ingesta-kafka` | Valentina | Consumers de `challenges.results` y `courses.lifecycle`, dedup y DLT | 🟡 PR #25 | ✅ Limpio (sus archivos de `processed_event` son idénticos a los de `develop`) |
| `feature/documentacion-contratos-parametros` | Luciano | Contrato canónico + contratos por tema | ⏳ Sin PR | **No publicar todavía** (H3) |

**Frontend** (`2026-PIV-TPI-FE`): en `develop` solo hay placeholders. Los slices hechos están en ramas sin mergear: 8 (ErrorApi, 34 commits de atraso), 7 (shell, 24), 1 (catálogo, **PR #31**), 2 (edición, 34), 4 (historial con mock, **133**), 6 (ingesta con endpoint adivinado, **133**). Los slices 3 (cuentas admin) y 5 no tienen rama remota.

### 1.3 🚨 Hallazgos

| # | Hallazgo | Estado | Lo arregla |
|---|---|---|---|
| **H1** | Los `GET` de parámetros solo aceptan `ADMIN`/`PROFESOR`: **ningún microservicio puede leernos** (403) | 🔴 Abierto | S1 |
| ~~H2~~ | ~~Seguridad `permitAll` en `develop`~~ | ✅ Resuelto por PR #27 | — |
| **H3** | Los contratos piden `X-User-Roles: MS` + `X-User-Id`, pero el filtro autentica servicios con `X-Principal-Type: service` + `X-Service-Id` + `X-Service-Scopes: READ_PARAMS`. **No publicar en Skill Hub hasta corregir** | 🔴 Abierto | Ana (doc) + revisión de Luciano |
| **H4** | Los contratos de T03 y T09 piden PAR-03/06/07, que son **externos** (T09 es proveedor, no consumidor) | 🔴 Abierto | Ana (doc) |
| **H5** | T05 y T07 usan PAR-19/20/22, que **no están sembrados** | 🔴 Abierto | S1 |
| ~~H6~~ | ~~Duplicados de Flyway en `us-08`~~ | ✅ Falso positivo (archivos idénticos) | — |
| ~~H7~~ | ~~`springdoc.packages-to-scan` pisado~~ | ✅ Resuelto (quedó `…p4`) | — |
| **H8** | El proxy del FE manda Tema 12 a `/api/v1/admin` (:8012), pero el backend expone `/api/administration/**` y `/api/reports/**` | 🔴 Abierto | S3 (config) |
| **H9** | `verify.yml` solo en `main`/`release/**`. **Decisión del equipo** (ahorrar minutos de Actions): no se cambia | ✅ Asumido | Gate local (§4.4) |
| **H10** | `OutboxMessage` no tiene columna `topic`: la alerta de deriva no puede ir a `system.notifications` | 🟡 P1 | S3 |
| **H11** | La gestión de administradores (US-03 T10) no está implementada, y `IdentityAuditClient` no tiene controller | 🔴 Abierto | S2 |
| **H12** | La propuesta de FE sugiere crear "G06-HU11", pero **HU11 ya existe** (#25) | 🔴 Abierto | Ana |
| **H13** | PR #22 agrega columnas de traza que **nadie completa**: los headers de Kafka salen `null` | 🔴 Abierto | S0 (Bruno): copiar `MDC.get(GatewayAuthenticationFilter.MDC_REQUEST_ID / MDC_TRACEPARENT)` en `ParameterChangeRecorderImpl` |

---

## 2. Alcance del MVP

| Pilar | Historias de Taiga | Slices |
|---|---|---|
| **Administración de plataforma** | HU03 (#182), HT04 (#1632), HT02 (#1630) | S2 Máximo · S3 Luciano |
| **Registro de parámetros PAR-01..24** | HU01 (#17), HU02 (#21) | S1 Damian · S3 Luciano (outbox) |
| **Proveedor LLM exclusivo ADMIN** | HU04 (#18), HU05 (#26), HU06 (#27), HU07 (#29) | S4 Regina · S5 Mateo · S6 Bruno · S8 Joaquin (casos) |
| **Contratos de lectura (6 temas)** | HU08 (#1628), HU10 (#28), HT01 (#1629) | S7 Valentina · S8 Joaquin (registro) |

**Fuera del MVP (Sprint 2):** HU11 (#25) read model de riesgo y HU12 (#24) panel docente, porque necesitan datos reales de T02, T03 y T08 que no fluyen y la API de pertenencia de T02 para RLS. Tampoco entran: las alertas `STUDENT_AT_HIGH_RISK` y de frescura hacia T11, el scheduler de deriva que le pide a T07 re-evaluar, el Slice 5 (monitor de Outbox) y US-02 T6.

---

## 3. Guion de la demo = criterio de aceptación global

> Se ensaya el domingo 27 (§8). Si un paso P0 falla, **el MVP no está terminado**.

| # | Actor | Acción | Resultado esperado | Integra con | Slice |
|---|---|---|---|---|---|
| 1 | ADMIN | Login (cookie `fu_at`) y entra a `/admin` | Dashboard con accesos y contadores en vivo | T01 (Gateway) | S3, S2 |
| 2 | ADMIN | Abre **Parámetros** | 24 filas: 14 confirmadas + 4 candidatas editables + 6 externas o suspendidas como referencia | — | S1 |
| 3 | ADMIN | Edita PAR-13 de 3 a 4 con motivo y confirma "aplica de ahora en adelante" | 200, versión 2. Reenvío con la misma `Idempotency-Key` → mismo resultado, sin versión 3 | — | S1 |
| 4 | ADMIN | Abre el historial de PAR-13 | v1 → v2 con diff, motivo, autor y fecha | — | S1 |
| 5 | Sistema | El outbox publica `GLOBAL_CONFIGURATION_CHANGED` (key `PAR-13`) con headers de traza | Evento en Kafka + auditoría en `identity.audit` | **T03, T11, T01** | S1, S0 |
| 6 | Otro MS | `GET /api/administration/parameters` con token M2M | 200 con `READ_PARAMS`; sin scope → 403 `ErrorApi` | **T03/T07 + Gateway** | S1, S2 |
| 7 | PROFESOR | Entra a Parámetros | Catálogo **sin edición**; un `PUT` forzado → 403 | — | S1, S2 |
| 8 | ADMIN | **LLM → Proveedores:** registra `anthropic` con API key | 201; la clave solo aparece como `sk-****abcd` | — | S4 |
| 9 | ADMIN | **LLM → Modelos:** registra `claude-sonnet` / EVALUATOR | 201 `PENDING_REVIEW`; "Activar" → **409** | — | S5 |
| 10 | ADMIN | **Golden set → Casos:** importa 5 casos | 201 con los 5 casos | — | S8 |
| 11 | ADMIN | **Golden set → Revisión:** ejecuta con los puntajes del modelo | MAE 3,4 ≤ PAR-14 (5) → `APPROVED`; otro modelo con 7,8 → `REJECTED` | — | S6 |
| 12 | ADMIN | Activa el modelo aprobado | `ACTIVE`, el anterior pasa a `RESERVE`, `MODEL_PROVIDER_CHANGED` en Kafka | **T07** | S5, S3 |
| 13 | T07 | `GET /api/administration/llm/models/active?function=EVALUATOR` con token M2M | Modelo activo + `credentialRef`, **sin clave** | **T07** | S4 |
| 14 | ADMIN/T07 | Revisión periódica del activo con error 6,2 | `DRIFT_DETECTED` → fallback al de reserva + evento con `DRIFT_FALLBACK` | **T07** | S6, S5 |
| 15 | ADMIN | **Contratos de lectura** | 7 tarjetas con estado del contrato, topic, último evento y frescura | — | S8, S7 |
| 16 | T03 / script | Publica `CHALLENGE_COMPLETED`, lo repite y manda uno malformado | Procesados +1, duplicados +1, DLT +1; T03 "al día" | **T03, T11** | S7 |
| 17 | ADMIN | **Administradores** y **Auditoría** | Otorgar/revocar con salvaguardas (auto-baja, último admin 409); auditoría lista los pasos 3 y 12 | **T01** | S2 |

---

## 4. Reglas anti-choque

### 4.1 Mapa de propiedad

Paquete base: `ar.edu.utn.frc.tup.p4`. Carpetas FE: `src/app/features/admin/slices/…`. **Si un archivo no está en tu fila, no lo tocás.**

| Slice | Dueño | Backend | Frontend |
|---|---|---|---|
| **S1** Parámetros | Damian | `administration.{controllers.ParameterController, services.GlobalParameter*, services.impl.GlobalParameter*, entities.GlobalParameter*, repositories.GlobalParameter*, dtos.parameters.*, exceptions.Parameter*/InvalidParameter*}` | `01-parameters-catalog/`, `02-parameter-mutation/`, `04-parameter-history/` |
| **S2** Seguridad, auditoría y cuentas | Máximo | `configs.{SecurityConfig, GatewayAuthenticationFilter, ErrorApiSecurityHandler, GatewayTrust*, T01Client*}`, `controllers.GlobalExceptionHandler`, `administration.{controllers.audit.*, services.IdentityAuditClient*, services.AdministrativeAudit*, dtos.identity.*, dtos.audit.*}` | `03-admin-accounts/`, `11-admin-audit/`, `core/guards/admin*.guard.ts` |
| **S3** Contratos compartidos, outbox y operación | Luciano | **PR de contratos compartidos** (§5.2), `administration.services.DomainEventOutbox*`, `administration.entities.OutboxMessage`, `administration.services.impl.OutboxPublisherServiceImpl`, `pom.xml`, `.compose/**`, `.github/**` | `admin.routes.ts`, `app.config.ts`, `07-admin-dashboard-shell/`, `08-resilience-error-api/`, `proxy.conf.json` |
| **S4** Proveedores LLM | Regina | `administration.*.llm.provider` (todas las capas) | `09-llm-providers/` |
| **S5** Modelos LLM y activación | Mateo | `administration.*.llm.model` (todas las capas) | `10-llm-models/` |
| **S6** Revisión, tolerancia y deriva | Bruno | `administration.*.llm.goldenset.run` (todas las capas) | `13-golden-set-runs/` |
| **S7** Ingesta y frescura | Valentina | `reporting.**` **excepto** `reporting.*.contracts` | `06-ingestion-monitor/` |
| **S8** Registro de contratos y casos del golden set | Joaquin | `reporting.*.contracts` + `administration.*.llm.goldenset.cases` | `12-golden-set-cases/`, `14-read-contracts/` |

> "Todas las capas" = `controllers.<sub>`, `services.<sub>`, `services.<sub>.impl`, `entities.<sub>`, `repositories.<sub>`, `dtos.<sub>`, `exceptions.<sub>`. Por ejemplo, S4 es dueña de `administration.controllers.llm.provider`, `administration.services.llm.provider`, etc.

### 4.2 Flyway: versiones reservadas

| Versión | Dueño | Contenido |
|---|---|---|
| V1–V5 | — | Ya en `develop` |
| **V6** | Bruno | `V6__outbox_message_trace_headers.sql` (PR #22) |
| **V7** | Damian | `V7__global_parameter_history.sql` (PR #29) |
| **V8, V9** | Damian (S1) | Metadatos del registro + seed de candidatos |
| **V10** | Luciano (S3) | **Esquema compartido LLM y golden set** (§5.2) |
| **V11** | Regina (S4) | Reserva (ajustes de `llm_provider`) |
| **V12** | Mateo (S5) | Reserva (ajustes de `llm_model`) |
| **V13** | Bruno (S6) | Reserva (ajustes de `golden_set_run*`) |
| **V14** | Joaquin (S8) | `reporting.source_contract` + seed de 7 filas |
| **V15** | Valentina (S7) | `reporting.ingested_event` + `reporting.ingestion_counter` |
| **V16** | Máximo (S2) | Reserva |
| **V17** | Luciano (S3) | Columna `topic` del outbox |

> Una migración mergeada **no se edita nunca** (Flyway valida el checksum). Si tu base local quedó "adelantada": `docker compose -f .compose/docker-compose.yml down -v`.
> **Base H2 de los tests:** no soporta índices parciales (`CREATE INDEX … WHERE`), y `value` es palabra reservada (citarla). La regla "un solo `ACTIVE` por función" se valida en el service.

### 4.3 Archivos compartidos: un solo dueño

| Archivo | Dueño | Cómo se pide un cambio |
|---|---|---|
| `pom.xml`, `.compose/**`, `application.properties` (transversal) | Luciano | Mensaje en el canal. Cada slice agrega **su propio bloque al final** (`##### S5 · LLM models #####`) |
| `SecurityConfig`, filtro, `GlobalExceptionHandler` | Máximo | Los demás solo usan `@PreAuthorize(SecurityExpressions.X)` y lanzan subclases de `BackofficeException` |
| Interfaces del PR de contratos compartidos (§5.2) | **Congeladas** desde el viernes 12:00 | Si hay que cambiar una, lo acuerdan en el canal el implementador y sus consumidores |
| `OutboxMessage` / `OutboxPublisherServiceImpl` | Luciano | Para emitir eventos se usa `DomainEventOutbox` |
| `admin.routes.ts` / `app.config.ts` (FE) | Luciano | Las rutas stub quedan el viernes; cada slice solo reemplaza **su** componente |

### 4.4 Ramas, PRs y gate local

- Nombres: **solo** `feature/…`, `fix/…` o `refactor/…`. Formato sugerido: `feature/mvp-s5-llm-models` (BE) y `feature/mvp-s5-llm-models-ui` (FE).
- **Cada slice abre al menos 1 PR de backend y 1 de frontend, ambos con sus tests.** Si un PR pasa ~400 líneas, se parte (primero entidades + migración, después endpoints).
- **Gate local (no hay Actions en `develop`):** `mvn clean verify` en verde (tests + Checkstyle + PMD + JaCoCo ≥ 90 %, con Docker levantado para Testcontainers). **Pegar en el PR** las líneas `Tests run: …, Failures: 0` y `BUILD SUCCESS`. El revisor lo vuelve a correr antes de aprobar. En el FE: `npm run test` + lint.
- Commits convencionales, **sin** "Co-Authored-By" ni firmas de IA. Todo en **inglés** (código, comentarios, `@DisplayName`, tests `method_scenario_expected`, `// GIVEN / WHEN / THEN`).
- Reglas que la IA suele romper: **sin `var`, sin `@Autowired`, sin `@Data` en entidades, sin FQCN, `ModelMapper` inyectado, colecciones vacías en vez de `null`, `BigDecimal` para dinero.**

### 4.5 Cómo trabajar con la IA

1. Pegale **el prompt base del Anexo D + el bloque de tu slice**.
2. Pedí primero **la lista de archivos** que va a crear o tocar y verificá que estén dentro de tu fila de §4.1.
3. TDD: tests del service con Mockito (**mockeando las interfaces de otros**), después MockMvc con headers del Gateway o Testcontainers.
4. Los endpoints y JSON de este documento **son el contrato**. Si algo no cierra, se pregunta en el canal y se actualiza este `.md`.

### 4.6 Balance de líneas efectivas

**Cómo se estimó:** con el tamaño real de archivos equivalentes del repo. Un controller de 3 endpoints ronda las 145 líneas; un service impl, 140–240; una entidad, 65–80; un DTO, 40–60; un repositorio, 30–45; una migración, 25–40; un test de service, 250–435; un slice FE, 374–973 con specs. **Líneas efectivas = código de producción + tests.** No cuentan docs, `.md`, `.http`, YAML ni JSON de configuración. Margen: ±30 %.

| Dev | Slice | Backend | Frontend | Tests (BE + FE) | **Total** |
|---|---|---:|---:|---:|---:|
| Damian | S1 Parámetros | ~350 | ~550 | ~1.100 | **~2.000** |
| Máximo | S2 Seguridad, auditoría y cuentas | ~350 | ~700 | ~1.000 | **~2.050** |
| Luciano | S3 Contratos compartidos, outbox y operación | ~900 | ~400 | ~600 | **~1.900** |
| Regina | S4 Proveedores LLM | ~800 | ~300 | ~850 | **~1.950** |
| Mateo | S5 Modelos LLM y activación | ~850 | ~350 | ~1.000 | **~2.200** |
| Bruno | S6 Revisión, tolerancia y deriva | ~900 | ~350 | ~1.000 | **~2.250** |
| Valentina | S7 Ingesta y frescura | ~800 | ~250 | ~1.000 | **~2.050** |
| Joaquin | S8 Registro de contratos y casos del golden set | ~800 | ~500 | ~850 | **~2.150** |
| | | | | **Total** | **~16.550** (promedio ~2.070, desvío máx. ±9 %) |

- **Todos** tienen backend, frontend y tests propios.
- Luciano queda en el extremo bajo porque suma coordinación y operación (no cuentan como código). Aun así está dentro del ±9 %.
- **Transparencia:** sumando lo ya aportado en el sprint (git desde el 13/09), Regina (~835) y Bruno (~715) siguen por debajo del resto en el total del sprint. Este reparto iguala **lo que falta**, no compensa lo anterior.

---

## 5. Antes de los slices

### 5.1 S0 · Tren de merges (jueves noche → viernes 20:00)

Cada autor resuelve los conflictos de su rama **en este orden**. Después de cada merge, quien mergea corre `mvn clean verify` sobre `develop`: **si queda en rojo, se frena el tren** y el que rompió arregla.

| Orden | Rama / PR | Resuelve | Revisa | Qué cuidar |
|---|---|---|---|---|
| ✅ | PR #27 (filtro) y PR #28 (regla de inglés) | — | — | Mergeados |
| 1 | PR #24 `feature/infra-platform-integration` | Luciano | Máximo | Limpio |
| 2 | PR #22 `feature/us-02-kafka-headers` | Bruno | Mateo | **H13** antes de salir de draft: completar las columnas desde el MDC, con test |
| 3 | PR #29 `feature/us-01-parametros` | Damian | Regina | Limpio. Trae `V7` |
| 4 | `feature/us-01-testcontainers` | Mateo | Damian | **Único conflicto:** gana `us-01-parametros`; adaptar el test de integración |
| 5 | `feature/us-02-envelope` | Mateo | Joaquin | Limpio. `EventEnvelopeFactory` la usa `DomainEventOutbox` (S3) |
| 6 | PR #25 `feature/us-08-ingesta-kafka` | Valentina | Joaquin | Limpio. Los listeners se cablean en S7 |
| FE | Rebase de las ramas FE: slice 8 → Luciano · 7 → Damian · 1 → Bruno (PR #31) · 2 → Joaquin · 4 → Mateo · 6 → Valentina | Autores | Nuevo dueño del slice | Rebase + PR. Desde ahí sigue el **dueño de §4.1** |

### 5.2 PR de "contratos compartidos" (Luciano, viernes ≤ 12:00)

> **Es la ÚNICA dependencia entre compañeros.** Es un PR chico (solo esquema, enums, interfaces, DTOs de intercambio, constantes y stubs de rutas), sin lógica. Una vez mergeado, **cada slice avanza solo**: usa las interfaces de otros y las mockea hasta que el implementador mergee.

**Backend:**

```text
V10__llm_and_golden_set_schema.sql
  administration.llm_provider(id UUID PK, name VARCHAR(60) UNIQUE NOT NULL, base_url VARCHAR(255),
      api_key_ciphertext TEXT NOT NULL, api_key_last4 VARCHAR(4) NOT NULL, status VARCHAR(20) NOT NULL,
      created_by VARCHAR(100), created_at TIMESTAMP, updated_at TIMESTAMP, version INT NOT NULL DEFAULT 0)
  administration.llm_model(id UUID PK, provider_id UUID NOT NULL REFERENCES llm_provider(id),
      model_name VARCHAR(100) NOT NULL, model_version VARCHAR(50) NOT NULL, llm_function VARCHAR(20) NOT NULL,
      temperature NUMERIC(3,2) NOT NULL, max_tokens INT NOT NULL, status VARCHAR(20) NOT NULL,
      created_by VARCHAR(100), created_at TIMESTAMP, updated_at TIMESTAMP, version INT NOT NULL DEFAULT 0,
      UNIQUE(provider_id, model_name, model_version, llm_function))
  administration.golden_set_case(id UUID PK, code VARCHAR(30) UNIQUE NOT NULL, prompt TEXT NOT NULL,
      reference_answer TEXT, reference_score NUMERIC(5,2) NOT NULL, dimension VARCHAR(50),
      active BOOLEAN NOT NULL DEFAULT TRUE, created_by VARCHAR(100), created_at TIMESTAMP)
  administration.golden_set_run(id UUID PK, model_id UUID NOT NULL REFERENCES llm_model(id),
      run_type VARCHAR(20) NOT NULL, mean_absolute_error NUMERIC(6,2), max_case_error NUMERIC(6,2),
      tolerance_average NUMERIC(6,2), tolerance_dimension NUMERIC(6,2), par14_version INT,
      verdict VARCHAR(30) NOT NULL, cases_evaluated INT NOT NULL, executed_by VARCHAR(100), executed_at TIMESTAMP)
  administration.golden_set_run_result(id UUID PK, run_id UUID NOT NULL REFERENCES golden_set_run(id),
      case_id UUID NOT NULL REFERENCES golden_set_case(id), model_score NUMERIC(5,2) NOT NULL,
      reference_score NUMERIC(5,2) NOT NULL, absolute_error NUMERIC(6,2) NOT NULL)

common.exceptions: BackofficeException(HttpStatus), ResourceNotFoundException(404),
                   BusinessConflictException(409), BusinessRuleViolationException(422),
                   ExternalServiceUnavailableException(503)          ← Máximo las mapea a ErrorApi en S2
configs.security.SecurityExpressions:
   ADMIN = "hasRole('ADMIN')"
   ADMIN_OR_PROFESOR = "hasAnyRole('ADMIN','PROFESOR')"
   PARAMETER_READERS = "hasAnyRole('ADMIN','PROFESOR') or hasAuthority('SCOPE_READ_PARAMS')"
   LLM_CONFIG_READERS = "hasRole('ADMIN') or hasAuthority('SCOPE_READ_PARAMS')"
Enums: LlmModelStatus {PENDING_REVIEW, APPROVED, REJECTED, ACTIVE, RESERVE, RETIRED}
       LlmFunction {TUTOR, EVALUATOR, GENERATOR, MODERATOR}
       GoldenSetRunType {CANDIDATE_REVIEW, PERIODIC_DRIFT}
       GoldenSetVerdict {APPROVED, REJECTED, WITHIN_TOLERANCE, DRIFT_DETECTED, DRIFT_DETECTED_NO_FALLBACK}
```

**Interfaces congeladas** (quién la implementa → quién la consume):

```java
// S3 Luciano implementa → S5 Mateo y S6 Bruno consumen
public interface DomainEventOutbox {
    /** Appends an event to the outbox inside the caller's transaction (propagation MANDATORY). */
    void append(OutboxEventRequestDto event); // aggregateType, aggregateId, partitionKey, eventType, payload, topic (null = administration.events)
}

// S4 Regina implementa → S5 Mateo consume (nombre del proveedor en respuestas y eventos)
public interface LlmProviderQuery {
    Optional<LlmProviderSummaryDto> findById(UUID providerId); // id, name, status, credentialRef
}

// S5 Mateo implementa → S6 Bruno y S4 Regina consumen
public interface LlmModelLifecycleService {
    LlmModelResponseDto getModel(UUID modelId);                                   // 404 if missing
    LlmModelResponseDto markReviewResult(UUID modelId, boolean withinTolerance);  // PENDING_REVIEW/REJECTED -> APPROVED/REJECTED
    Optional<LlmModelResponseDto> findActive(LlmFunction function);
    ModelActivationResponseDto switchToFallback(LlmFunction function, String reason); // emits MODEL_PROVIDER_CHANGED
}

// S8 Joaquin implementa → S6 Bruno consume
public interface GoldenSetCaseQuery {
    List<GoldenSetCaseDto> findActiveCases(); // id, code, referenceScore, dimension
}

// S7 Valentina implementa → S8 Joaquin consume
public interface IngestionStatsQuery {
    List<SourceIngestionStatsDto> findAllSourceStats(); // sourceTeam, lastEventAt, eventsProcessed,
                                                        // duplicatesDiscarded, deadLettered, stale,
                                                        // minutesSinceLastEvent, thresholdMinutes
}
```

**Frontend:** rutas stub finales en `admin.routes.ts`, cada una con un componente placeholder **dentro de la carpeta del dueño**: `''` → 07 · `parameters` → 01 · `parameters/:key/history` → 04 · `users` → 03 · `audit` → 11 · `llm/providers` → 09 · `llm/models` → 10 · `llm/golden-set/cases` → 12 · `llm/golden-set/runs` → 13 · `ingestion` → 06 · `contracts` → 14. Redirects: `llm-providers` → `llm/providers` y `calibration` → `llm/golden-set/runs`.

**Dependencias que quedan después de este PR:**

| Consumidor | Usa | En el código | En runtime (demo) |
|---|---|---|---|
| S5, S6 | `DomainEventOutbox` (S3) | Mock en unit tests | Necesita la impl de S3 mergeada |
| S5 | `LlmProviderQuery` (S4) | Mock | Idem S4 |
| S4, S6 | `LlmModelLifecycleService` (S5) | Mock | Idem S5 |
| S6 | `GoldenSetCaseQuery` (S8) | Mock | Idem S8 |
| S8 | `IngestionStatsQuery` (S7) | Mock | Idem S7 |
| S6 (FE) | Listas de modelos y casos | Mock en specs | Llama a `GET …/llm/models` y `GET …/golden-set/cases` |
| S3 (FE dashboard) | Contadores | Mock en specs | Llama a los listados de S1, S5 y S8; muestra "—" si no responden |

**Nadie espera código de otro para programar ni para testear su unidad.** La integración real se prueba en CP3–CP4 (domingo).

---

## 6. Slices (cada uno: backend + frontend + tests)

---

### S1 · Registro de parámetros PAR-01..24 — Damian

**Revisa:** Regina · **HU:** HU01 (#17), HU02 (#21) · **Ramas:** `feature/mvp-s1-parameter-registry` / `-ui` · **~2.000 líneas**

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 1.1 | `V8__global_parameter_registry_metadata.sql`: columnas `status` (`CONFIRMED`/`CANDIDATE`), `value_type` y `consumers`. Seed de **PAR-19 (30), PAR-20 (48), PAR-22 (3) y PAR-23 (15)** como `CANDIDATE`, **con su fila v1 en `global_parameter_history`**. **Nunca** PAR-03/06/07/12/21/24 | BE | P0 |
| 1.2 | `ParameterResponseDto` + `name`, `description`, `status`, `consumers`, `updatedAt` (retrocompatible, sin `updatedBy`) | BE | P0 |
| 1.3 | **H1:** GETs con `SecurityExpressions.PARAMETER_READERS`; PUT sigue `ADMIN` | BE | P0 |
| 1.4 | `GET /api/administration/parameters/{key}/history` (ADMIN) con `previousValue` | BE | P0 |
| 1.5 | Rangos de PAR-19/20/22/23 (`InvalidParameterValueException`) | BE | P0 |
| 1.6 | Tests: historial, rangos, roles (ADMIN, PROFESOR, servicio con y sin scope, anónimo), **idempotencia CA2 (Testcontainers)**, payload del outbox igual al contrato canónico con key `PAR-XX` | Test | P0 |
| 1.7 | FE: integrar slices 1, 2 y 4 rebaseados; modelo TS con los campos nuevos; sección **"Parámetros externos (solo referencia)"** con constante estática (PAR-03/06/07 → T09, PAR-12 → T08, PAR-24 → T01, PAR-21 suspendido) → **24 filas** | FE | P0 |
| 1.8 | FE: modal con `Idempotency-Key` UUID v4 **única por sesión del modal**, motivo ≥ 10 caracteres, confirmación de no retroactividad; historial `/parameters/:key/history` con diff (**borrar el mock** `/v1/admin/audit`); PROFESOR solo lectura | FE | P0 |
| 1.9 | Specs Vitest: 24 filas, PROFESOR sin botones, misma key al reintentar, diff del historial | Test | P0 |

```http
GET /api/administration/parameters                 → ADMIN | PROFESOR | SCOPE_READ_PARAMS
200 [ { "key": "PAR-13", "name": "Máximo de reintentos", "description": "…", "value": 3, "version": 1,
        "status": "CONFIRMED", "consumers": ["T03"], "updatedAt": "2026-09-26T15:00:00Z" } ]
GET /api/administration/parameters/{key}/history   → ADMIN
200 [ { "version": 2, "value": 4, "previousValue": 3, "reason": "Adjusted after teacher feedback",
        "changedBy": "admin-001", "changedAt": "…" } ]
```

---

### S2 · Seguridad, auditoría y cuentas de administradores — Máximo

**Revisa:** Valentina · **HU:** HU03 (#182), HT04 (#1632) · **Ramas:** `feature/mvp-s2-platform-security` / `-ui` · **~2.050 líneas**

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 2.1 | `GlobalExceptionHandler`: **un solo** handler `BackofficeException` → `ErrorApi` con su status. 401 sin headers y 403 por rol, ambos en `ErrorApi` | BE | P0 |
| 2.2 | `GET /api/administration/audit` (ADMIN) → `IdentityAuditClient` → T01 `GET /api/users/audit` (`resourceType`, `from`, `to`, `offset`, `limit`). T01 caído → 503 | BE | P0 |
| 2.3 | `AdministrativeAuditPublisher` (interfaz + impl + DTO tipado) para auditar acciones LLM en `identity.audit`. S4 y S5 lo llaman (P1); si no está mergeado, lo mockean | BE | P1 |
| 2.4 | **`AuthorizationMatrixIntegrationTest`** parametrizado con el Anexo C (rol × endpoint). Suma filas a medida que mergean los demás slices. Es el único test cross-slice | Test | P0 |
| 2.5 | Tests del handler, del endpoint de auditoría (T01 mockeado con respuestas 200 y 503) y del publisher | Test | P0 |
| 2.6 | FE: guards `adminGuard` (llm/\*, contracts, ingestion, users, audit, historial) y `adminOrProfesorGuard` (parameters) según `AuthService.roles()` | FE | P0 |
| 2.7 | FE `11-admin-audit`: tabla (actor, acción, recurso, fecha) con filtros, contra 2.2 | FE | P0 |
| 2.8 | FE `03-admin-accounts`: tabla + otorgar/revocar **directo a T01** (`/api/admin/accounts/*` por el Gateway). Auto-revocación deshabilitada; 409 del último admin con aviso. Validar el viernes la forma real con Postman | FE | P1 |
| 2.9 | Specs de guards, auditoría y salvaguardas de cuentas | Test | P0 |

---

### S3 · Contratos compartidos, outbox genérico y operación — Luciano (+ coordinación)

**Revisa:** Máximo · **HU:** HT02 (#1630), HT04 (#1632), HU02 (#21) · **Ramas:** `feature/mvp-s3-shared-contracts`, `feature/mvp-s3-outbox`, `feature/mvp-s3-admin-shell-ui` · **~1.900 líneas**

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 3.1 | **PR de contratos compartidos (§5.2)**, viernes ≤ 12:00 | BE+FE | P0 |
| 3.2 | `DomainEventOutboxImpl`: fila `OutboxMessage` con `aggregateType`, `aggregateId`, `paramKey` = partition key, `eventType`, envelope de `EventEnvelopeFactory` y traza desde el MDC | BE | P0 |
| 3.3 | **H10:** `V17` con la columna `topic` nullable en el outbox. El publisher usa el topic de la fila o `administration.events` por defecto. Permite mandar `LLM_MODEL_DRIFT_DETECTED` a `system.notifications` | BE | P1 |
| 3.4 | Tests: append dentro de la transacción (falla sin transacción), traza copiada, enrutamiento por topic (Testcontainers) | Test | P0 |
| 3.5 | FE: merge del slice 8 (interceptor ampliado a `/api/administration/*` y `/api/reports/*`) + slice 7 (dashboard) con **contadores en vivo** (parámetros, modelos por estado, contratos desactualizados) | FE | P0 |
| 3.6 | Specs del dashboard (contadores y "—" cuando un endpoint falla) y del interceptor filtrado | Test | P0 |
| — | **Operación (no cuenta como código):** coordinar S0; compose local con el backoffice en **:8012**; **H8** en `proxy.conf.json` (`/api/administration` y `/api/reports` → `:8012` antes del fallback `/api`, con headers de demo); ruta del Gateway, Tailscale y Vault con T01; registrar con T11 `MODEL_PROVIDER_CHANGED` y `LLM_MODEL_DRIFT_DETECTED`; correr el E2E del domingo | Ops | P0/P1 |

---

### S4 · Proveedores LLM y cifrado de claves — Regina

**Revisa:** Mateo · **HU:** HU04 (#18, parte proveedores) · **Ramas:** `feature/mvp-s4-llm-providers` / `-ui` · **~1.950 líneas**

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 4.1 | Entidad `LlmProvider` + repositorio sobre la tabla de V10 | BE | P0 |
| 4.2 | `ApiKeyCipher` AES-256-GCM con la clave desde la variable `LLM_API_KEY_ENCRYPTION_KEY` (base64, 32 bytes; en despliegue viene de Vault vía `app.env`). Enmascarado `sk-****abcd`. **Nunca** se loguea ni aparece en `toString` | BE | P0 |
| 4.3 | `POST/GET /api/administration/llm/providers` (ADMIN); 409 por nombre duplicado | BE | P0 |
| 4.4 | Implementar `LlmProviderQuery` (con `credentialRef = "vault:secret/tpi/llm/<name>"`) | BE | P0 |
| 4.5 | `GET /api/administration/llm/models/active?function=` (`LLM_CONFIG_READERS`) para T07: combina `LlmModelLifecycleService.findActive` (mock hasta que S5 mergee) con el proveedor. **Sin clave** | BE | P1 |
| 4.6 | Tests: roundtrip del cipher, **ninguna respuesta contiene la clave en claro** (`doesNotContain(rawKey)`, CA3), duplicado 409, endpoint para T07 con scope | Test | P0 |
| 4.7 | FE `09-llm-providers`: alta (clave como password, nunca se vuelve a mostrar) + lista enmascarada | FE | P0 |
| 4.8 | Specs del formulario y de que la clave no se renderiza | Test | P0 |

```http
POST /api/administration/llm/providers   (ADMIN)
{ "name": "anthropic", "baseUrl": "https://api.anthropic.com", "apiKey": "sk-ant-XXXXXXXXXXXXabcd" }
201 { "id": "…", "name": "anthropic", "baseUrl": "…", "apiKeyMasked": "sk-****abcd", "status": "ACTIVE", "createdAt": "…" }
GET  /api/administration/llm/models/active?function=EVALUATOR   (ADMIN | SCOPE_READ_PARAMS)
200 { "function": "EVALUATOR", "provider": "anthropic", "modelName": "…", "modelVersion": "…",
      "temperature": 0.2, "maxTokens": 800, "credentialRef": "vault:secret/tpi/llm/anthropic" }
```

---

### S5 · Modelos LLM y activación — Mateo

**Revisa:** Bruno · **HU:** HU04 (#18, parte modelos), HU05 (#26) · **Ramas:** `feature/mvp-s5-llm-models` / `-ui` · **~2.200 líneas**

**Ciclo de vida:** `PENDING_REVIEW` → (revisión OK) `APPROVED` / (revisión mala) `REJECTED` · `APPROVED`/`RESERVE` → (activar) `ACTIVE` y el activo anterior pasa a `RESERVE` · `ACTIVE` con deriva → `REJECTED` y se activa el fallback · cualquier estado salvo `ACTIVE` → `RETIRED`. **Un solo `ACTIVE` por función.** Activar desde otro estado → 409.

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 5.1 | Entidad `LlmModel` (`providerId` como UUID, **sin** relación JPA con `LlmProvider`) + repositorio | BE | P0 |
| 5.2 | `POST/GET /api/administration/llm/models` (ADMIN, filtros `function` y `status`); `providerName` sale de `LlmProviderQuery` | BE | P0 |
| 5.3 | `POST …/models/{id}/activate` en **una** transacción: estados + `MODEL_PROVIDER_CHANGED` vía `DomainEventOutbox` (key `LLM-<FUNCTION>`); 409 si no es `APPROVED`/`RESERVE`. `POST …/{id}/retire` (409 si está activo) | BE | P0 |
| 5.4 | Implementar `LlmModelLifecycleService` (`markReviewResult`, `findActive`, `switchToFallback` con `reason = DRIFT_FALLBACK`) | BE | P0 |
| 5.5 | Tests: transiciones válidas e inválidas, un solo activo, 409 (CA2 HU05), el evento lleva el modelo anterior y el nuevo (CA3), fallback sin reserva disponible | Test | P0 |
| 5.6 | FE `10-llm-models`: alta, tabla con badges de estado (color **y** texto), filtro por función, "Activar" solo en `APPROVED`/`RESERVE`, "Retirar"; 409 al banner | FE | P0 |
| 5.7 | Specs de habilitación de botones y manejo del 409 | Test | P0 |

```json
{ "eventId": "uuid", "eventType": "MODEL_PROVIDER_CHANGED", "eventVersion": 1,
  "timestamp": "…", "producer": "tema-12-backoffice-service",
  "payload": { "function": "EVALUATOR",
    "previousModel": { "id": "…", "provider": "openai", "modelName": "gpt-4o-mini", "modelVersion": "2024-07-18" },
    "newModel": { "id": "…", "provider": "anthropic", "modelName": "claude-sonnet", "modelVersion": "2026-06" },
    "reason": "MANUAL_SWITCH", "actorId": "admin-001", "role": "ADMIN", "correlationId": "…", "changedAt": "…" } }
```

---

### S6 · Revisión con golden set, tolerancia PAR-14 y deriva — Bruno

**Revisa:** Damian · **HU:** HU06 (#27, parte corridas), HU07 (#29) · **Ramas:** `feature/mvp-s6-golden-set-runs` / `-ui` · **~2.250 líneas**

**Regla:** el Backoffice **nunca invoca LLMs**. La corrida recibe los **puntajes que produjo el modelo** (los manda T07 o, en la demo, los carga el ADMIN).

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 6.1 | Entidades `GoldenSetRun` y `GoldenSetRunResult` (IDs de modelo y caso como UUID, sin relación JPA) + repositorios | BE | P0 |
| 6.2 | `POST /api/administration/llm/golden-set/runs`: modelo vía `LlmModelLifecycleService.getModel` (404); casos vía `GoldenSetCaseQuery` (si no hay, 422); **los resultados deben cubrir todos los casos activos** (si no, 422 con los faltantes); MAE = promedio de \|modelScore − referenceScore\| | BE | P0 |
| 6.3 | `ToleranceEvaluator`: lee PAR-14 con `GlobalParameterService.findByKey` (`{promedio, dimension}`) y **guarda la versión usada**. Veredicto: MAE ≤ promedio **y** error máximo por caso ≤ dimension | BE | P0 |
| 6.4 | Candidato → `markReviewResult`. `ACTIVE` (corrida `PERIODIC_DRIFT`) que excede → `switchToFallback` + `LLM_MODEL_DRIFT_DETECTED` vía `DomainEventOutbox` | BE | P0 / P1 (deriva) |
| 6.5 | `GET …/golden-set/runs?modelId=` y `GET …/runs/{id}` con el detalle por caso | BE | P0 |
| 6.6 | Tests con los números de Taiga: 3,4 → aprobado; 7,8 → rechazado; 6,2 en activo → fallback. Borde MAE = 5,0 → aprobado; sin casos → 422; incompleto → 422; PAR-14 mockeado | Test | P0 |
| 6.7 | FE `13-golden-set-runs`: formulario (modelo + puntaje por caso, o importar JSON), tabla de corridas (MAE, tolerancia usada, veredicto con color **y** texto) y detalle | FE | P0 |
| 6.8 | Specs del formulario y de los veredictos | Test | P0 |

```http
POST /api/administration/llm/golden-set/runs   (ADMIN)
{ "modelId": "…", "results": [ { "caseCode": "GS-01", "modelScore": 77 } ] }
201 { "id": "…", "runType": "CANDIDATE_REVIEW", "casesEvaluated": 5, "meanAbsoluteError": 3.4,
      "maxCaseError": 6.0, "toleranceAverage": 5, "toleranceDimension": 10, "par14Version": 1,
      "verdict": "APPROVED", "modelStatusAfter": "APPROVED", "executedAt": "…" }
```

---

### S7 · Ingesta y frescura — Valentina

**Revisa:** Joaquin · **HU:** HU08 (#1628), HU10 (#28) · **Ramas:** `feature/mvp-s7-ingestion` / `-ui` · **~2.050 líneas**

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 7.1 | Listeners de `challenges.results` y `courses.lifecycle` cableados con topics desde propiedades y `group.id = tema-12-backoffice-group` | BE | P0 |
| 7.2 | `V15`: `reporting.ingested_event` (append-only: `event_id`, `source_team`, `topic`, `event_type`, `producer`, `occurred_at`, `received_at`, `payload` JSONB) + `reporting.ingestion_counter` (`source_team` PK, `events_processed`, `duplicates_discarded`, `dead_lettered`, `last_event_at`) | BE | P0 |
| 7.3 | `IngestionRecorder.record(sourceTeam, envelope, outcome)` llamado desde los consumers (`PROCESSED`/`DUPLICATE`/`DEAD_LETTERED`) | BE | P0 |
| 7.4 | Implementar `IngestionStatsQuery` con **frescura** (HU10): `stale = now − last_event_at > PAR-23` (15 si no existe, vía `GlobalParameterService`), calculado al leer. `GET /api/reports/health/freshness` y `GET /api/reports/ingestion/events?sourceTeam=&limit=50` (ADMIN) | BE | P0 |
| 7.5 | Listeners de T08 `economy.transactions` y T10 `sandbox.events` detrás de flags (`ingestion.sources.t08.enabled=false`) que guardan el envelope crudo | BE | P1 |
| 7.6 | Tests: dedup, DLT, contadores, frescura en el borde (15 min = al día, 16 = desactualizado), PAR-23 ausente → 15, listeners con flag | Test | P0 |
| 7.7 | FE `06-ingestion-monitor`: resumen por tema (último evento, badge de frescura con texto) + tabla de últimos eventos. **Borrar el endpoint adivinado** | FE | P0 |
| 7.8 | Specs del resumen y la tabla | Test | P0 |

---

### S8 · Registro de contratos de lectura y casos del golden set — Joaquin

**Revisa:** Luciano · **HU:** HT01 (#1629), HU06 (#27, parte casos) · **Ramas:** `feature/mvp-s8-contracts-and-cases` / `-ui` · **~2.150 líneas**

| # | Tarea | Capa | Prioridad |
|---|---|---|---|
| 8.1 | `V14`: `reporting.source_contract` (`source_team` único, `source_name`, `topic`, `mechanism`, `event_types`, `contract_status`, `contract_version`) + seed de 7 filas (tabla abajo) | BE | P0 |
| 8.2 | `GET /api/reports/contracts` (ADMIN): combina el registro con `IngestionStatsQuery` (mock hasta que S7 mergee) | BE | P0 |
| 8.3 | Entidad `GoldenSetCase` + repositorio; `POST` (carga en lote, 409 por `code` duplicado) y `GET /api/administration/llm/golden-set/cases` (ADMIN) | BE | P0 |
| 8.4 | Implementar `GoldenSetCaseQuery` | BE | P0 |
| 8.5 | Tests: combinación registro + estadísticas (tema sin eventos, tema desactualizado), carga en lote, duplicados, validación de `referenceScore` en 0..100 | Test | P0 |
| 8.6 | FE `14-read-contracts`: una tarjeta por tema (estado del contrato, mecanismo, topic, último evento, badge de frescura con texto, contadores) | FE | P0 |
| 8.7 | FE `12-golden-set-cases`: tabla + importar JSON (con validación) | FE | P0 |
| 8.8 | Specs de tarjetas e importación | Test | P0 |

| source_team | topic | mecanismo | event_types | contract_status |
|---|---|---|---|---|
| T02 Cursos | `courses.lifecycle` | KAFKA | `ROSTER_UPDATED`, `SURVEY_SUBMITTED` | `REQUEST_READY` |
| T03 Desafíos | `challenges.results` | KAFKA | `CHALLENGE_COMPLETED` | `AGREED` |
| T04 Teóricos | — | NONE | — (encuestas pasaron a T02) | `NO_CONTRACT` |
| T05 Prácticos | a definir | KAFKA | — | `PENDING` |
| T07 Evaluación LLM | a definir con T11 | KAFKA | calibración, deriva, presupuesto | `IN_PROGRESS` |
| T08 Banco | `economy.transactions` + REST `/api/bank/**` | KAFKA/REST | saldo | `AGREED` |
| T10 Roadmap | `sandbox.events` | KAFKA | progreso, niveles | `IN_PROGRESS` |

---

## 7. Ana Paula (MSII) · toda la documentación, Taiga y diagramas

> La documentación **no cuenta como líneas de código** y queda centralizada en Ana para que los devs solo programen. Luciano revisa la exactitud técnica de los contratos. Ana es la **única** que mueve Taiga.

| # | Tarea | Cuándo |
|---|---|---|
| A1 | Mover al milestone `G06 - Sprint 1`: HU04 (#18), HU05 (#26), HU06 (#27), HU07 (#29) y HU10 (#28). HU11 y HU12 quedan en backlog | Viernes AM |
| A2 | Crear las tareas del **Anexo A** con responsable. **No** crear "G06-HU11 observabilidad" (H12) | Viernes AM |
| A3 | Verificar HT02 (#1630): suma 3 puntos cerrados pero figura "New" | Viernes |
| A4 | **Corregir contratos (H3/H4/H5)** en `feature/documentacion-contratos-parametros`: token M2M + `X-Principal-Type: service`/`X-Service-Scopes: READ_PARAMS` (sin `X-User-Roles: MS`); quitar PAR-03 de T03 y reescribir T09 como proveedor; PAR-19/20/22/23 como `CANDIDATE`; agregar a T07 `MODEL_PROVIDER_CHANGED` + `GET …/llm/models/active`. **Recién después** se publica en Skill Hub | Viernes–sábado |
| A5 | `docs/demo/DEMO-MVP.md` (guion de §3) + fixture `docs/demo/golden-set-cases.json` + `docs/demo/publish-test-events.md` | Sábado |
| A6 | Firmas G4 en `CONTRATOS.md` (T11, T03, T07) | Sábado–domingo |
| A7 | DER con las tablas nuevas, diagrama de estados de `LlmModel` y BPMN de aprobación de modelo | Sábado–domingo |
| A8 | Checklist de la demo + minuta de la sprint review + cierre de Taiga | Domingo 20:00 |

> Cada dev agrega **su** sección de requests en `docs/demo/mvp.http` (lo usa su revisor). No cuenta como código.

---

## 8. Calendario con checkpoints

| Cuándo | Qué pasa | Checkpoint |
|---|---|---|
| **Jue 24 · noche** | Todos leen el plan. S0 órdenes 1–3 | **CP0 · 23:59:** todos confirman su slice |
| **Vie 25** (estudio para algunos) | **12:00: PR de contratos compartidos mergeado.** S0 órdenes 4–6 + rebase FE. Mensajes de integración cruzada. Ana carga Taiga | **CP1 · 20:00:** `develop` en verde (verify local) + ramas de slice creadas |
| **Sáb 26 · parcial** | Después del parcial: cada slice implementa su backend y frontend con IA, con interfaces mockeadas | **CP2 · 23:59:** PR de backend de cada slice abierto, tests en verde |
| **Dom 27 · mañana** | Reviews cruzadas y merges | **CP3 · 12:00:** freeze de backend · **14:00:** freeze de FE |
| **Dom 27 · tarde** | 14–17: E2E completo sobre `develop` (primera vez que se prueban las integraciones reales). 17–19: **solo fixes** | **CP4 · 19:00:** guion §3 pasa en P0 |
| **Dom 27 · noche** | Ensayo de la demo + cierre de Taiga | **CP5 · 20:30** |

**Regla de corte:** si en CP2 un slice no tiene PR, se entrega su P0 mínimo y el resto va a Sprint 2.

---

## 9. Revisión cruzada

Cada uno revisa a un compañero y es revisado por otro. Se buscó que el revisor sea **consumidor** del código que revisa (conoce el contrato).

| Autor | Revisor | Por qué |
|---|---|---|
| Damian (S1) | Regina | Criterio de seguridad |
| Máximo (S2) | Valentina | Coautora del filtro (PR #15) |
| Luciano (S3) | Máximo | Constantes de seguridad y excepciones del PR compartido |
| Regina (S4) | Mateo | Consume `LlmProviderQuery` |
| Mateo (S5) | Bruno | Consume `LlmModelLifecycleService` |
| Bruno (S6) | Damian | Lee PAR-14 del registro |
| Valentina (S7) | Joaquin | Consume `IngestionStatsQuery` |
| Joaquin (S8) | Luciano | Esquema V10 compartido |

La revisión incluye **correr** `mvn clean verify` y la sección del autor en `mvp.http`, no solo leer el diff.

---

## 10. Decisiones que el equipo tiene que confirmar

| # | Decisión | Recomiendo | Alternativa y costo |
|---|---|---|---|
| **D1** | Dónde vive la API key LLM | Cifrada en nuestra BD (AES-GCM) con la clave de cifrado desde Vault; T07 recibe solo `credentialRef` | Solo referencia a Vault: bloqueado por la API de Vault de T01, que está pendiente |
| **D2** | Quién produce los puntajes del golden set | Llegan en el request (T07 o ADMIN) | Pedirle a T07 que evalúe: contrato EN CURSO |
| **D3** | Rutas LLM | `/api/administration/llm/**` (las de Taiga) | `/model-providers` o `/llm-providers`: hay que unificar |
| **D4** | Cuentas de administradores | FE → Gateway → T01 directo | Backoffice como proxy: más código, sin valor extra |
| **D5** | Nombre en Eureka | `backoffice-service` (código + `AGENTS.md`); tiene que coincidir con la ruta del Gateway | `tema-12-backoffice-service`: toca código |
| **D6** | Scope para T07 | Reusar `READ_PARAMS` | `READ_LLM_CONFIG`: requiere que T01 lo emita |
| **D7** | PAR externos y suspendido | Constante estática en el FE | Endpoint de metadatos: roza la regla de no sembrar externos |
| **D8** | Alerta de deriva | `system.notifications` si entra V17 (S3 3.3); si no, `administration.events` | — |
| **D9** | Desacople por interfaces (§5.2) | Sí: es lo que permite que 8 personas avancen sin esperarse | Relaciones JPA entre slices: menos código, pero cada uno depende de la clase del otro |

---

## 11. Riesgos y plan B

| Riesgo | Señal | Plan B |
|---|---|---|
| El PR de contratos compartidos se atrasa | Sin PR a las 11:00 del viernes | Cada dev copia **exactamente** las interfaces de §5.2 en su rama. Son idénticas, así que mergean sin conflicto |
| S0 no cierra el viernes | CP1 en rojo | El autor + Luciano en pair con IA; los demás arrancan sobre la rama del PR compartido |
| La integración real falla el domingo (interfaces con distinta semántica) | E2E 14:00 | Por eso hay 3 horas de fixes; los consumidores revisan a los implementadores (§9) |
| El Gateway de T01 no enruta | Sin respuesta el sábado | Demo por el proxy local con headers de demo; M2M con `curl` |
| T11 no materializa los topics | Sin respuesta el sábado | Kafka del compose + script de eventos de prueba |
| Entra un test roto a `develop` (no hay CI ahí) | Verify local en rojo | Se frena el tren hasta arreglarlo; corrida completa en CP1 y CP4 |
| La IA toca archivos fuera del slice | Diff con archivos de otra fila de §4.1 | La revisión rechaza el PR |

---

## Anexo A · Carga en Taiga (para Ana)

> Formato: `(prioridad) tarea — responsable`. Cada dev tiene al menos una tarea BACK, una FRONT y una TEST.

**HU01 (#17) · Parámetros** — (P0) V8 metadatos + candidatos — Damian · (P0) lectura para microservicios `READ_PARAMS` — Damian · (P0) endpoint de historial — Damian · (P0) tests de roles, historial e idempotencia — Damian · (P0) FE catálogo 24 + externos — Damian · (P0) FE edición + historial + solo lectura — Damian · (P0) specs FE — Damian

**HU02 (#21) · Propagación** — (P0) headers de traza en el outbox (PR #22, H13) — Bruno · (P0) `DomainEventOutbox` genérico — Luciano · (P1) enrutamiento por topic (V17) — Luciano

**HU03 (#182) · Administración de plataforma** — (P0) handler `BackofficeException` → `ErrorApi` — Máximo · (P0) matriz de autorización — Máximo · (P0) endpoint de auditoría vía T01 — Máximo · (P1) `AdministrativeAuditPublisher` — Máximo · (P0) FE guards — Máximo · (P0) FE auditoría — Máximo · (P1) FE cuentas admin (T01) — Máximo

**HT04 (#1632) · Front conectado** — (P0) PR de contratos compartidos + rutas stub — Luciano · (P0) interceptor + dashboard con contadores — Luciano

**HU04 (#18) · Proveedores y modelos** — (P0) proveedores + cifrado + `LlmProviderQuery` — Regina · (P1) endpoint de modelo activo para T07 — Regina · (P0) FE proveedores — Regina · (P0) catálogo de modelos — Mateo · (P0) FE modelos — Mateo

**HU05 (#26) · Conmutación** — (P0) activación + `MODEL_PROVIDER_CHANGED` + `LlmModelLifecycleService` — Mateo

**HU06 (#27) · Golden set** — (P0) casos + `GoldenSetCaseQuery` — Joaquin · (P0) FE casos — Joaquin · (P0) corridas + MAE — Bruno · (P0) FE corridas — Bruno

**HU07 (#29) · Tolerancia y deriva** — (P0) `ToleranceEvaluator` con PAR-14 — Bruno · (P1) deriva → fallback + alerta — Bruno

**HU08 (#1628) · Ingesta** — (P0) listeners + `IngestionRecorder` + contadores — Valentina · (P1) listeners T08/T10 con flags — Valentina · (P0) FE monitor de ingesta — Valentina

**HU10 (#28) · Frescura** — (P0) `IngestionStatsQuery` con PAR-23 + endpoint de frescura — Valentina

**HT01 (#1629) · Contratos** — (P0) registro `source_contract` + endpoint — Joaquin · (P0) FE contratos de lectura — Joaquin · (P0) corrección de contratos H3/H4/H5 — Ana · (P1) firmas G4 — Ana

**HT02 (#1630) · Infra** — (P0) compose + proxy FE (H8) — Luciano · (P1) Gateway + Tailscale + Vault — Luciano · (P0) gate local en la plantilla de PR — Luciano

**HT03 (#1631) · Documentación de diseño** — (P1) DER + estados de `LlmModel` + BPMN + guion de demo — Ana

---

## Anexo B · DoD del MVP

- ☐ Guion §3: todos los pasos P0 pasan sobre `develop` (CP4).
- ☐ `mvn clean verify` en verde sobre `develop` (corrida local), JaCoCo ≥ 90 %. `verify.yml` sin cambios.
- ☐ Cero `permitAll` fuera de los endpoints públicos; la matriz del Anexo C pasa completa.
- ☐ Cero mocks en `data-access/` del FE de backoffice.
- ☐ Swagger muestra Parámetros, LLM, Golden set, Auditoría y Reportes.
- ☐ Contratos corregidos y publicados; `CONTRATOS.md` actualizado.
- ☐ Ninguna API key en claro en respuestas, logs, tests ni repo.
- ☐ Cada dev tiene al menos un PR de backend y uno de frontend mergeados, con tests.
- ☐ Taiga refleja el estado real y `docs/Task/tareas-sprint1.md` tiene la asignación final.

---

## Anexo C · Matriz de autorización (la usa S2 en su test)

| Endpoint | ADMIN | PROFESOR | ALUMNO | Servicio + `READ_PARAMS` | Servicio sin scope | Sin headers |
|---|---|---|---|---|---|---|
| `GET /api/administration/parameters[/{key}]` | 200 | 200 | 403 | 200 | 403 | 401 |
| `PUT /api/administration/parameters/{key}` | 200 | 403 | 403 | 403 | 403 | 401 |
| `GET /api/administration/parameters/{key}/history` | 200 | 403 | 403 | 403 | 403 | 401 |
| `/api/administration/llm/providers`, `/llm/models` (salvo `active`) | 2xx | 403 | 403 | 403 | 403 | 401 |
| `GET /api/administration/llm/models/active` | 200 | 403 | 403 | 200 | 403 | 401 |
| `/api/administration/llm/golden-set/**` | 2xx | 403 | 403 | 403 | 403 | 401 |
| `GET /api/administration/audit` | 200 | 403 | 403 | 403 | 403 | 401 |
| `GET /api/reports/**` | 200 | 403 | 403 | 403 | 403 | 401 |

---

## Anexo D · Prompt base para la IA (pegar ANTES del bloque de tu slice)

```text
You are working on the Backoffice microservice (Tema 12) of a university platform.
Repository rules are in AGENTS.md and are mandatory: Java 21, Spring Boot 4.0.0,
package root ar.edu.utn.frc.tup.p4, constructor injection with @RequiredArgsConstructor,
NO var, NO @Autowired, NO @Data on JPA entities, NO fully qualified class names, ModelMapper
injected (never new), empty collections instead of null, BigDecimal for money, English only in
code/comments/test names, errors always as ErrorApi{timestamp,status,error,message}.
Boot 4: use @MockitoBean (not @MockBean); @AutoConfigureMockMvc lives in
org.springframework.boot.webmvc.test.autoconfigure.
Tests: JUnit 5 + Mockito (@ExtendWith(MockitoExtension.class)) for units, MockMvc with the
Gateway headers (X-Principal-Type, X-User-Id, X-User-Roles, X-Service-Id, X-Service-Scopes)
for controllers, Testcontainers for integration. Naming method_scenario_expected, AAA with
// GIVEN // WHEN // THEN, boundary values and exception paths. Coverage >= 90%.
The plan of record is docs/Task/plan-mvp-sprint1-backoffice.md. You may ONLY create or modify
files listed for my slice in section 4.1, and ONLY the Flyway versions reserved for my slice in 4.2.
The interfaces in section 5.2 are FROZEN: implement the ones my slice owns exactly as written,
and use the others only through their interface, mocking them in unit tests. Never reference
another slice's entity or implementation class.
Before writing code, list every file you will create or modify and wait for my confirmation.
Never call an LLM provider. Never log secrets. Do not run builds unless I ask.
```
