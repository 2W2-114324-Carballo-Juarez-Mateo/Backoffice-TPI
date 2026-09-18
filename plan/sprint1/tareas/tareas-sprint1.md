# Tareas Sprint 1 — Backlog dividido (10 integrantes) · CONSOLIDADO

> **Decisiones confirmadas en planning:**
> - **Repo de entrega:** `2026-P4-BE/tpi-backoffice` — mono-módulo, Spring Boot 4.0.0, Java 21. **1 datasource + 2 esquemas** (`administration` / `reporting`).
> - **Mensajería:** el formato oficial de cátedra (`EventoDTO` 5 campos + headers Kafka) queda **PENDIENTE de confirmar con Usuarios/cátedra**; mientras tanto se mantiene el envelope actual.
> - **Frontend canónico:** el repo **`/FE` del Demo** (`TPI---Backoffice-Demo-/FE/`); si `backoffice-angular` aporta algo clave (Docker/Nginx) se suma.
> - **US-03:** se **agregan las tareas de gestión de rol** (consumir `/api/admin/accounts` de T01) para cubrir sus 4 CA.
> - **DoD Nivel 1:** el criterio **"multitenancy + RLS" se acota** a historias con datos por `course_id` (reportes), no a todas.
> - **Scaffolding/Infra (PR #0): dueño = Luciano (Dev 1).** Tests de integración: por otra persona (no el autor). **Ana Paula (Dev 10)**: solo MSII (docs/diagramas/contratos).

---# Tareas Sprint 1 — Backlog dividido (10 integrantes)

> **Repo de entrega:** `2026-P4-BE/tpi-backoffice` — **mono-módulo**, Spring Boot 4.0.0, Java 21, paquete base `ar.edu.utn.frc.tup.p4`. **Decidido.**
> **Alcance:** Infra + US-01 + US-02 + US-03 + US-08 (acotada) + EP-04 Contratos + **Frontend** + Documentación de diseño.
> **Flujo de ramas:** `feature/<algo>` o `fix/<algo>` desde `develop` → **PR a `develop`** → `release/*` o `hotfix/*` a `main`. El workflow `branching-name-check` de cátedra rechaza cualquier otra combinación.
> **Estimación:** historia = SP (Fibonacci), tarea = horas.

---

## 1 · Capacidad real del equipo

La planilla tenía la fórmula rota (`#REF!`). Recalculada:

**`Teóricas = h/día × (días − ausencias)` → `Efectivas = teóricas − 8,5 (ceremonias)` → `Ajustada = efectivas × % dedicación`**

| Integrante | Rol | h/día | Días | Aus. | Teóricas | Ceremonias | Efectivas | % | **Ajustada** |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Paz, Luciano | MSII+PIV | 5,0 | 10 | 0 | 50,0 | 8,5 | 41,5 | 95 % | **39,4** |
| Carballo Juarez, Mateo | MSII+PIV | 5,0 | 10 | 0 | 50,0 | 8,5 | 41,5 | 100 % | **41,5** |
| Baigorria, Damian Gabriel | PIV | 5,0 | 10 | 2 | 40,0 | 8,5 | 31,5 | 100 % | **31,5** |
| Cortez, Joaquin | PIV | 5,0 | 10 | 2 | 40,0 | 8,5 | 31,5 | 88 % | **27,7** |
| Ducart, Ana Paula | MSII | 5,0 | 10 | 2 | 40,0 | 8,5 | 31,5 | 80 % | **25,2** |
| Maldonado, Valentina | MSII+PIV | 5,0 | 10 | 0 | 50,0 | 8,5 | 41,5 | 85 % | **35,3** |
| Cerquatti, Máximo | MSII+PIV | 6,0 | 10 | 0 | 60,0 | 8,5 | 51,5 | 90 % | **46,4** |
| Disca, Julieta Ariadna | MSII+PIV | 6,0 | 10 | 0 | 60,0 | 8,5 | 51,5 | 100 % | **51,5** |
| Cerasulo, Regina Loreta | MSII+PIV | 5,0 | 10 | 0 | 50,0 | 8,5 | 41,5 | 85 % | **35,3** |
| Gianoli, Bruno | PIV | 4,0 | 10 | 0 | 40,0 | 8,5 | 31,5 | 90 % | **28,4** |
| **TOTAL** | | | | **6** | **480,0** | **85,0** | **395,0** | | **362,1** |

**Capacidad real del equipo: 362,1 h.** No 431 — la diferencia son las 85 h de ceremonias y los porcentajes de dedicación.

### Qué cambia respecto de lo que veníamos asumiendo

- **Máximo: 46,4 h reales.** Trabaja los **10 días hábiles normales** con 0 ausencias. En la planilla figuraba "14 días y 4 ausencias" (días corridos restando el fin de semana). Con 6 h/día y 90 % de dedicación, es el segundo con más capacidad — por eso carga los tests de integración pesados.
- **Ana Paula tiene 25,2 h, no 45.** Es **MSII pura**: no codifica. Su bloque se recortó a lo que entra.
- **Los roles importan para asignar.** PIV = código. MSII = proceso, documentación, coordinación. MSII+PIV = ambos.

---

## 2 · Alcance: qué entra y qué no, y por qué

El inventario completo de trabajo daba **296 h** sobre 362 h de capacidad = **82 % de uso**. Eso no es un sprint, es un sprint sin margen para nada. El objetivo sano es **65–70 %**.

Hubo que cortar 59 h: **31 h** de US-04, **11 h** de acotar US-08 y **18 h** de comprimir Infra. El criterio:

### ✅ Entra completo — es el circuito de la demo

**Infra · US-01 · US-02 · US-03 · Frontend**

La demo tiene que mostrar: **ADMIN entra por el front → ve los parámetros → cambia uno → se persiste con versión e historial → se emite el evento a Kafka.** Todo lo que está en ese circuito entra entero.

### 🔶 Entra acotada — US-08 (Ingesta)

Solo los consumidores con **contrato en acuerdo**: T03 (`challenge.events`) y T02 (`course.events`). Los de T07 y T10 salen: sus contratos **no están cerrados** y programar contra un esquema que no existe es trabajo que se tira.

### ❌ Sale del Sprint 1 — US-04 (Modelos de IA)

**Recomendación, para decidir en el planning.** Los motivos, en orden:

1. **No cierra valor.** Quien aprueba un modelo (`PENDING_REVIEW → APPROVED`) es **US-07**, que no está en el sprint. Tal como estaba, entregábamos un catálogo donde ningún modelo puede activarse jamás.
2. **No es parte de la demo.** No aparece en el circuito end-to-end que queremos mostrar.
3. **Tiene un bloqueante sin resolver.** Vault de T01 vs cifrado local: dos diseños incompatibles, y la API de Vault de T01 está pendiente. No tiene Definition of Ready.
4. **Libera 31 h** — casi exactamente lo que cuesta el frontend, que sí es parte de la demo.

Va al **Sprint 2 junto con US-07**, que es quien le da sentido.

### Total planificado

**241 h sobre 362,1 h = 66,6 % de uso.** Queda ~121 h de margen para lo que no vimos venir.

| Bloque | Horas |
|---|---:|
| US-02 · Outbox + Kafka | 54 |
| US-01 · Parámetros | 50 |
| Frontend | 34 |
| US-08 · Ingesta (acotada) | 30 |
| US-03 · Borde de seguridad + rol | 30 |
| EP-04 · Contratos | 17 |
| Documentación de diseño | 20 |
| **Infra** | **6** |
| **Total** | **241** |

---

## 3 · El frontend entra al sprint

Estaba diferido a TH-03 con la nota "fuera de la capacidad del sprint de Back". **Eso era un error**: sin front no hay demo de un MVP conectado.

### El punto de partida real

Ya existen **9 pantallas Angular** funcionando (Angular 21, standalone, signals, zoneless, Vitest). Lo que **no existe** es la capa de datos:

- No hay `provideHttpClient` en `app.config.ts`.
- **Cero** llamadas HTTP en todo el proyecto.
- Cero guards, cero interceptores, cero `environment`.
- Los 18 parámetros están **hardcodeados en un signal**, y `saveParam()` muta el array en memoria e imprime un toast que dice "Evento emitido a Kafka" — que es texto, no una llamada.

O sea: la maqueta está hecha. Falta enchufarla.

### Bloque Frontend — 34 h (partido para que los 9 devs toquen FRONT)

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| FE-1a | **Capa HTTP:** interceptor de errores (mapea `ErrorApi` de cátedra) + `provideHttpClient` | FRONTEND | Luciano | 3 |
| FE-1b | **Capa HTTP:** interceptor de headers (`Idempotency-Key`, correlación) + `environment` | FRONTEND | Máximo | 3 |
| FE-2a | Servicio HTTP de parámetros (GET/PUT) | FRONTEND | Mateo | 4 |
| FE-2b | **Conectar la pantalla existente** (reemplazar los 18 PAR hardcodeados) | FRONTEND | Bruno | 4 |
| FE-3a | **Guard ADMIN** (escribe) | FRONTEND | Regina | 3 |
| FE-3b | **Guard PROFESOR** (solo lectura) + estado de sesión | FRONTEND | Valentina | 3 |
| FE-4a | Pantalla de administración: asignar/revocar rol | FRONTEND | Bruno | 3 |
| FE-4b | Pantalla de administración: estado 2FA/sesión | FRONTEND | Julieta | 3 |
| FE-5a | Feedback de operación: loading + error | FRONTEND | Damian | 2 |
| FE-5b | Feedback de operación: confirmación "aplica de ahora en adelante" | FRONTEND | Joaquin | 2 |
| FE-6 | **Smoke test E2E del flujo de la demo** | TEST | Regina | 4 |

> **Decisión tomada:** el repo **canónico del front es `TPI---Backoffice-Demo-/FE/`** (el que tiene proxy y deploy a Pages). `backoffice-angular/` (Dockerfile/Nginx) aporta solo si sacamos algo clave de ahí — se revisa el Día 1 sin bloquear el bloque FE.

---

## 4 · Infra · Configuración del scaffolding de cátedra — 6 h

El scaffolding es **inicial**: trae el esqueleto y la calidad, y lo demás lo configuramos nosotros.

> **Dueño inicial del PR #0: Luciano.** Centralizar la línea base del `pom.xml` el Día 1 evita que múltiples desarrolladores choquen con merge conflicts en Git y colisiones de versiones (Classpath Hell). **El scaffolding inicial lo hace Luciano; una vez mergeado el cimiento común (PR #0/#1), la configuración, dependencias y el resto las puede editar cualquiera** con el flujo normal `feature/* → PR → review → develop`, cuando la tarea lo requiera.
> **Rama:** `feature/infra-bootstrap` · **PR #0 · merge Día 2** · **Bloquea a todo el resto.**

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| I0a | **Adopción del repo + dependencias base:** completar `.tpi/.tpi` con los 10 integrantes, `app.dev-name`, `app.dev-email`, README. Decidir la estructura de paquetes. Agregar al `pom.xml`: `spring-kafka`, `flyway-core` + `flyway-database-postgresql`, driver **PostgreSQL**, `spring-boot-starter-security`, `spring-cloud-starter-netflix-eureka-client` (Discovery Client), **Testcontainers**, **JaCoCo** | INFRA | Luciano | 3 |
| I0b | **`.compose/docker-compose.yml`** — hoy tiene **0 bytes**: PostgreSQL, Kafka y la app con discovery configurable | INFRA | Luciano | 2 |
| I0c | **Extender `verify.yml` a PRs contra `develop`** + umbral de JaCoCo | INFRA | Luciano | 1 |

### Dónde fue a parar la configuración

Infra queda con lo **estrictamente transversal**. La configuración de cada herramienta viaja con la historia que la necesita — que además es mejor ownership:

| Configuración | Dónde vive ahora |
|---|---|
| Perfiles, datasource PostgreSQL, `ddl-auto=validate`, Flyway sobre dos esquemas | **US-02 T1a** (Luciano) — la primera migración es quien lo necesita. Pasa de 3 h a **5 h** |
| Spring Security | **US-03 T1** (Luciano) — ya estaba dentro |
| Testcontainers | **US-02 T5** (Máximo) y **US-01 T8** (Julieta) — ya estaban dentro |
| Alinear `springdoc.packages-to-scan` | **US-01 T9** (Joaquin) — el dueño del OpenAPI es quien sufre si sale vacío |
| Cliente de discovery (Eureka Client) | **I0a** (Luciano) — dependencia y config base para que `tpi-api-gateway` descubra al microservicio |

> ⚠️ **6 h alcanzan si Luciano arranca sabiendo exactamente qué configurar.** Las dos cosas que típicamente comen tiempo son **Flyway sobre dos esquemas** y **Kafka en compose**. Si el Día 2 el PR #0 no mergea, todo el sprint se corre — es el riesgo a mirar en la daily del Día 1.

### Decisiones de arquitectura que impone el mono-módulo

**Una aplicación, un datasource, dos esquemas.**
El plan tenía `administration_db` y `reporting_db`. En mono-módulo con un solo artefacto desplegable eso no existe. Se resuelve con **un datasource y dos esquemas** (`administration` y `reporting`) en la misma base. Mantiene la frontera lógica, deja la puerta abierta a separar en el futuro, y **resuelve de paso el problema de los dos `ProcessedEvent`: es uno solo**, con columna `consumer`.

**Servidores de Eureka, Config Server y Gateway salen del alcance del Backoffice.**
No levantamos infraestructura de servidores centrales (el Gateway y los servidores de plataforma son de T01). Lo que nos corresponde como microservicio dentro de la red es ser **Discovery Client** (`spring-cloud-starter-netflix-eureka-client` en `pom.xml`) para que el `tpi-api-gateway` pueda descubrir y enrutar las peticiones hacia `tpi-backoffice`. En local corre con fallback o flag deshabilitable.

**`verify.yml` hay que extenderlo o el gate no existe.**
El workflow de cátedra corre solo en PRs a `main` y `release/**`. Todo nuestro flujo es `feature/* → develop`. Tal como está, **ninguno de nuestros PRs dispara CI.** Eso es I0c.

---

## 5 · Mensajería — el contrato cambia

El `EventoDTO` oficial de cátedra, **compartido por todos los microservicios**:

```java
public record EventoDTO(
    String eventId,
    String eventType,        // SCREAMING_SNAKE_CASE, en castellano
    LocalDateTime timestamp, // ISO 8601 UTC
    String producer,         // "tema-12-backoffice"
    Map<String, Object> payload
) {}
```

| Lo que decía el plan | Lo que corresponde |
|---|---|
| `EventEnvelope` propio | Consumir `com.utn.tpi.common.dto.EventoDTO` |
| `occurredAt` · `source` | `timestamp` · `producer` |
| `correlationId`, `actorId`, `role` en el envelope | **Headers de Kafka** (metadatos de transporte) |
| Eventos en inglés (`ParameterChanged`, `GlobalConfigurationChanged`) | **Ratificado en inglés** por cátedra |
| Topics en inglés (`challenge.events`, `course.events`, `administration.events`) | **Ratificado en inglés** por cátedra |

> **G1 es la tarea más urgente del sprint.** Enviar a T11 para formalizar la registración en la red.

---

## 6 · Resumen de carga (los 9 devs cubren las 5 capas)

| Dev | Integrante | Rol | Capacidad | Horas | % uso | Código (BACK+FRONT) | Capas |
|---|---|---:|---:|---:|---:|---|
| **1** | Paz, Luciano | MSII+PIV | 39,4 | 29 | 73,6 % | 19 h | BACK + FRONT + TEST + REV + DOC (camino crítico) |
| **2** | Carballo Juarez, Mateo | MSII+PIV | 41,5 | 25 | 60,2 % | 14 h | BACK + FRONT + TEST + REV + DOC |
| **3** | Baigorria, Damian Gabriel | PIV | 31,5 | 25 | 79,4 % | 14 h | BACK + FRONT + TEST + REV + DOC |
| **4** | Cortez, Joaquin | PIV | 27,7 | 22 | 79,4 % | 14 h | BACK + FRONT + TEST + REV + DOC |
| **5** | Disca, Julieta Ariadna | MSII+PIV | 51,5 | 28 | 54,4 % | 17 h | BACK + FRONT + TEST + REV + DOC |
| **6** | Maldonado, Valentina | MSII+PIV | 35,3 | 27 | 76,5 % | 17 h | BACK + FRONT + TEST + REV + DOC |
| **7** | Cerquatti, Máximo | MSII+PIV | 46,4 | 26 | 56,0 % | 17 h | BACK + FRONT + TEST + REV + DOC |
| **8** | Cerasulo, Regina Loreta | MSII+PIV | 35,3 | 19 | 53,8 % | 10 h | BACK + FRONT + TEST + REV + DOC |
| **9** | Gianoli, Bruno | PIV | 28,4 | 20 | 70,4 % | 10 h | BACK + FRONT + TEST + REV + DOC |
| **10** | Ducart, Ana Paula | MSII | 25,2 | 20 | 79,4 % | 0 h | DOC (solo MSII: diagramas, contratos, planificación) |

**Total: 241 h / 362,1 h = 66,6 %.** Horas ~54–79 % (dentro de capacidad) · código ~10–19 h · **los 9 devs cubren las 5 capas (BACK + FRONT + TEST + REV + DOC)**; Ana Paula solo DOC (MSII).

> **Regla de la división pareja:** los 9 que codifican tocan **las 5 capas** (BACK, FRONT, TEST, REV, DOC). Para alcanzar a todos se **partieron** FE-1/2/3/4/5 (10 subtareas de front), US-02 T5 (T5a/T5b) y las revisiones (T9a/T9b, T10a/T10b, T7a/T7b, T8a/T8b/T8c) — **nadie revisa ni testea su propio código**. Solo los unitarios de dominio pueden ser del autor (US-01 T7). **Ana Paula (Dev 10)** solo MSII: docs, diagramas y contratos.
> **Luciano** lleva el camino crítico (Infra PR #0, migración PR #1, publisher) y aun así cubre las 5 capas.

---

## 7 · US-02 · Propagación del cambio de parámetro (Outbox + Kafka) — 54 h

> **Rama:** `feature/us-02-outbox` · **PR #2 · merge Día 4** · **Depende de:** Infra I0a + G1.
> **Migración `V1__outbox_message.sql` va en PR #1 propio, merge Día 2.**

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1a | Migración Flyway de `outbox_message` + **perfiles, datasource PostgreSQL y Flyway sobre dos esquemas** — **PR propio #1** | BACKEND | Luciano | 5 |
| T1b | Publisher programado (Outbox → Kafka) **con clave de partición = clave del parámetro** | BACKEND | Luciano | 5 |
| T2 | Adoptar el `EventoDTO` oficial y mapear `ParameterChanged` / `GlobalConfigurationChanged` | BACKEND | Julieta | 6 |
| T3 | Reintentos con backoff exponencial y Dead Letter Topic | BACKEND | Mateo | 6 |
| T4 | Idempotencia por `eventId` y versión en el consumidor de referencia | BACKEND | Mateo | 4 |
| T5a | Test de integración del ciclo **Outbox → publicación** | TEST | Máximo | 4 |
| T5b | Test de integración del ciclo **consumo** (deduplicación) | TEST | Luciano | 4 |
| T6 | Validar resiliencia ante caída del broker | TEST | Julieta | 6 |
| T7 | Validar descarte de duplicados y de versión anterior | TEST | Joaquin | 4 |
| T8 | Documentar envelope, catálogo de topics y contrato del consumidor | DOCUMENTACION | Luciano | 4 |
| T9a | Peer review de **concurrencia** del outbox (revista trabajo de otro) | REVISION | Damian | 2 |
| T9b | Peer review de **transaccionalidad** del outbox (revista trabajo de otro) | REVISION | Joaquin | 1 |
| **T11** | **NUEVA** · `X-Request-Id`/`traceparent` como **headers de Kafka** | BACKEND | Bruno | 3 |

**Sin clave de partición, US-02 CA4 no se cumple:** Kafka reparte round-robin y se pierde el orden de versiones por parámetro.
**T5 pasó de Mateo a Máximo** para que el test de integración no lo escriba quien escribió el código.

---

## 8 · US-01 · Modificación y versionado de parámetros globales — 50 h

> **Rama:** `feature/us-01-parametros` · **PR #4 · merge Día 7** · **Depende de:** US-02 T1a (PR #1).
> **Migración:** `V2__global_parameter.sql`

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1 | Migración y entidad `GlobalParameter` con historial + **seed PAR-01..18 sin PAR-03/06/07/12** | BACKEND | Damian | 6 |
| T2 | Caso de uso `UpdateParameterCommand` con versionado y no retroactividad | BACKEND | Damian | 6 |
| T3 | Endpoints REST GET/PUT con autorización por rol | BACKEND | Joaquin | 6 |
| T4 | Persistir el registro en `outbox_message` en la misma transacción | BACKEND | Joaquin | 6 |
| T7 | Tests unitarios de dominio: versionado y rango | TEST | Damian | 6 |
| T8 | Tests de integración con Testcontainers | TEST | Mateo | 6 |
| T9 | Congelar contrato OpenAPI 3 | DOCUMENTACION | Joaquin | 3 |
| T10a | Peer review y validación de **DoD/CA de US-01** (revista trabajo de otro) | REVISION | Luciano | 2 |
| T10b | Peer review del **versionado/no retroactividad** de US-01 (revista trabajo de otro) | REVISION | Julieta | 1 |
| **T11** | **NUEVA** · Emisión del evento de auditoría — contrato **cerrado** con T01 | BACKEND | Regina | 4 |
| **T12** | **NUEVA** · `Idempotency-Key` en el endpoint + tabla de claves + TTL | BACKEND | Valentina | 4 |

> Insumo para el seed y validaciones: `plan/PARAMETROS.md`. Para valores compuestos como PAR-01 o curvas de niveles, se valida la estructura del JSON en el Command.

---

## 9 · US-03 · Borde de seguridad e integración de identidades — 26 h

> **Rama:** `feature/us-03-gateway-auth` · **PR #3 · merge Día 5** · **Depende de:** Infra I0a (Security).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1 | Filtro de seguridad e inspección de headers del Gateway | BACKEND | Máximo | 4 |
| T2 | Cliente HTTP hacia Tema 01 vía Gateway (auditoría delegada) | BACKEND | Máximo | 6 |
| T5 | Tests de integración del filtro y autorización por headers | TEST | Valentina | 6 |
| T7 | Matriz de delegación de identidades con Tema 01 | DOCUMENTACION | Regina | 4 |
| T8a | Auditoría de **fronteras de seguridad** (revista trabajo de otro) | REVISION | Valentina | 1 |
| T8b | Auditoría del **manejo de errores** (`ErrorApi`, revista trabajo de otro) | REVISION | Mateo | 1 |
| T8c | Auditoría de la **gestión de rol** (revista trabajo de otro) | REVISION | Regina | 1 |
| **T9** | **NUEVA** · `@RestControllerAdvice` sobre el DTO **`ErrorApi` de cátedra** | BACKEND | Regina | 3 |
| **T10** | **NUEVA** · Gestión de rol desde el panel vía T01 (`/api/admin/accounts`): asignar/revocar rol, auto-revocación 400, último admin 409, aviso con admins restantes — **cubre los 4 CA de US-03** | BACKEND | Máximo | 4 |

> ✅ **Decisión tomada:** se **agregan las tareas de gestión de rol** (T10) para que US-03 sea **demostrable** (cubre sus 4 CA: asignar rol → 201, auto-revocación → 400, último admin → 409, aviso de admins restantes). No se renombra la historia.

> **Sobre T9:** el repo de cátedra ya provee `ErrorApi{timestamp, status, error, message}`. **Ese es el formato**, no el RFC 7807 del doc de arquitectura ni los otros dos que andan dando vueltas. Hay cuatro formatos de error documentados en el proyecto; gana el de cátedra.

---

## 10 · US-08 · Ingesta de datos — 30 h (acotada)

> **Rama:** `feature/us-08-ingesta-kafka` · **PR #5 · merge Día 8** · **Depende de:** Infra I0a + G1.
> **Migración:** `V3__processed_event.sql`

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1 | Migración y tabla de deduplicación `processed_event` | BACKEND | Julieta | 4 |
| T2a | Consumidores de **`challenge.events`** (T03) y **`course.events`** (T02) | BACKEND | Valentina | 6 |
| T3 | Deduplicación por `eventId` | BACKEND | Valentina | 4 |
| T4 | Dead Letter Topic para eventos malformados | BACKEND | Julieta | 4 |
| T5 | Tests de integración: ingesta, deduplicación y DLT | TEST | Bruno | 6 |
| T6 | Mapear contratos de lectura y esquemas de los temas con acuerdo | DOCUMENTACION | Valentina | 3 |
| T7a | Peer review de consumidores — **idempotencia** (revista trabajo de otro) | REVISION | Máximo | 2 |
| T7b | Peer review de consumidores — **Dead Letter Topic** (revista trabajo de otro) | REVISION | Bruno | 1 |

> ✅ **Reparto resuelto.** Había **tres versiones contradictorias** de qué consumidores construir. Queda una sola:
> - **Entran:** T03 (`challenge.events`) y T02 (`course.events`) — los únicos topics con contrato acordado/en curso.
> - **Salen T07 y T10:** contratos sin cerrar. Entran cuando G3 y el de T10 se cierren.
> - **T04 fuera:** no tiene contrato; las encuestas pasaron a T02.
> - **"T06" fuera:** no existe como tema partner en ningún documento del proyecto.
> - **T08 (Banco) no es consumer Kafka:** se ingiere por REST `/api/bank/**` + evento de saldo, pendiente de confirmar.

---

## 11 · EP-04 · Registro de Contratos — 17 h

> **Rama:** `docs/contratos-sprint1` · sin merge bloqueante.
> La épica existía **sin una sola tarea**. Este bloque la implementa.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| **G1** | **Solicitud de contrato de mensajería a T11** — topics, envelope, tipo de `timestamp`, headers, artefacto compartido, naming del payload | DOCUMENTACION | Mateo | 4 |
| G2 | Solicitud de contrato a **T05** — entregas/resultados, topic, PAR-19/20 | DOCUMENTACION | Damian | 3 |
| G3 | Solicitud de contrato a **T07** — proveedor de modelo, deriva/calibración, PAR-22 | DOCUMENTACION | Máximo | 3 |
| G4 | **Registro formal en `CONTRATOS.md`** — estado, fecha de acuerdo, responsable de cada lado, versión y evidencia | DOCUMENTACION | Julieta | 4 |
| G5 | Tabla de mapeo topics oficiales ↔ Backoffice + plan de migración | DOCUMENTACION | Bruno | 3 |

**G1 se manda el Día 1, antes que nada.**

**Sobre G4:** hoy `CONTRATOS.md` registra *estado* ("cerrado", "acuerdo", "pendiente") pero no registra *firma*: no dice quién acordó de cada lado, cuándo, sobre qué versión, ni dónde está la evidencia. Si un tema cambia su payload en el Sprint 2, no hay forma de demostrar qué se había acordado.

---

## 12 · Documentación de diseño — 20 h

> **Responsable principal:** Ana Paula (MSII) · **Rama:** `docs/diagramas-sprint1`
> Detalle y checklist por diagrama en [dev-10.md](dev-10.md).

| # | Tarea | Rol | Dev | Horas | Día |
|---|---|---|---|---:|---|
| AP-1 | BPMN · cambio de parámetro global end-to-end | DOCUMENTACION | Ana Paula | 5 | 1–3 |
| AP-2 | Diagrama de Microservicios — ubica al Tema 12 y sus topics en la red | DOCUMENTACION | Ana Paula | 4 | 1–3 |
| AP-3 | Diagrama Entidad-Relación (DER) de ambos esquemas | DOCUMENTACION | Ana Paula | 5 | 2–4 |
| AP-4 | Diagrama de Estados · OutboxMessage y Parameter | DOCUMENTACION | Ana Paula | 3 | 4–5 |
| AP-6 | Carga y sincronización en Taiga + contratos | DOCUMENTACION | Ana Paula | 3 | 9–10 |

**Diferido a Sprint 2:** Diagramas de Secuencia, Diagrama de Clases, BPMN de ingesta. Los motivos están en `dev-10.md`.

---

## 13 · Secuencia de merges

| PR | Rama | Contenido | Merge |
|---:|---|---|---|
| #0 | `feature/infra-bootstrap` | pom, perfiles, compose, CI, adopción del repo | Día 2 |
| #1 | `feature/us-02-outbox-ddl` | Solo la migración `outbox_message` | Día 2 |
| #2 | `feature/us-02-outbox` | Publisher, envelope, DLT, idempotencia | Día 4 |
| #3 | `feature/us-03-gateway-auth` | Filtro, cliente T01, manejo de errores, **gestión de rol (CA1-4)** | Día 5 |
| #4 | `feature/us-01-parametros` | Parámetros, outbox tx, auditoría, idempotencia | Día 7 |
| #5 | `feature/us-08-ingesta-kafka` | Consumidores, dedup, DLT | Día 8 |
| #6 | `feature/fe-conexion-backend` | Capa HTTP, guards, pantallas conectadas | Día 9 |

> El PR #1 existe porque **T1a debe mergear el Día 2 y la rama de US-02 recién cierra el Día 4**. Con un solo PR era imposible: la migración bloquea a US-01 T4.

---

## 14 · Decisiones resueltas en planning

1. **T11 — contrato de mensajería (G1):** se envía el Día 1; el formato de evento queda **PENDIENTE de confirmar con Usuarios/cátedra** (DTO oficial vs envelope actual).
2. **Repo Angular canónico:** **`/FE` del Demo** (resuelto).
3. **US-04:** **sale del Sprint 1** → va al Sprint 2 con US-07 (resuelto).
4. **US-03:** se **agregan las tareas de gestión de rol (T10)** para cubrir los 4 CA (resuelto).
5. **DoD Nivel 1 «multitenancy + RLS»:** **se acota** a historias con datos por `course_id` (reportes) (resuelto).
6. ~~¿Cobertura 80 % o 90 %?~~ **Decidido: 90 %, el de nuestro DoD.** El 80 % del PR template de cátedra es un piso, no un techo.
7. **División:** **los 9 devs cubren las 5 capas** (BACK + FRONT + TEST + REV + DOC); Ana Paula solo MSII/DOC (ver §6).

---

> **DoD:** checklist unificado al pie de cada `dev-N.md`. Es el mismo para los 10.
> **Contratos y parámetros:** `plan/CONTRATOS.md` · `plan/PARAMETROS.md`.
