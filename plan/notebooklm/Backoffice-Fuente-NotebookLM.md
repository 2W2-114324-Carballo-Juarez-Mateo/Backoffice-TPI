# Backoffice (Tema 12) — Fuente de documentación para NotebookLM

> **Proyecto:** Plataforma de Aprendizaje Gamificado de Programación y Desarrollo de Software.
> **Módulo:** Backoffice (Tema 12 de la división oficial de 12 temas).
> **Rol del módulo:** "consumidor puro: sin dominio propio". Administra la plataforma y reporta, pero NO es dueño de identidad, cursos, desafíos ni economía: los **consume** o los **lee** mediante contratos.
> **Documento de referencia oficial:** `TUP_PIV_BE_PROPUESTA_ARQ.pdf` + PRD de la plataforma.

---

## 1. Contexto del proyecto

Plataforma de e-learning gamificada para enseñar programación. Cada curso se representa como un roadmap de aprendizaje; los estudiantes avanzan resolviendo desafíos teóricos y prácticos, acumulan XP, monedas, vidas e insignias, compiten en un ranking por curso, y son asistidos por agentes de IA con restricciones pedagógicas. Tiene chat, notificaciones, encuestas de satisfacción y una economía gamificada.

La plataforma se construye como **microservicios**, dividida en **12 temas** (asignación oficial del docente). Cada tema es un equipo. Nuestro equipo es el **Tema 12 — Backoffice**.

### Los 12 temas
- **T01 — Identidad y Usuarios:** registro, autenticación, 2FA, roles/permisos, token/sesión, auditoría, retención y **API Gateway de plataforma**.
- **T02 — Cursos y Matrícula:** curso-cohorte (la instancia concreta de un curso en un cuatrimestre), inscripción, padrón, invitación.
- **T03 — Motor de Desafíos:** ciclo de vida de los desafíos, entregas, estados.
- **T04 — Teóricos y Encuestas:** ítems teóricos, corrección, encuestas de satisfacción (anónimas).
- **T05 — Desafíos Prácticos:** consignas de código, casos de prueba, sandbox.
- **T06 — Sandbox / Runtime:** ejecución aislada de código.
- **T07 — Evaluación LLM:** rúbricas, golden set, calibración, invocación de modelos.
- **T08 — Banco:** XP, monedas, saldos por curso, ledger de movimientos.
- **T09 — Mercado:** catálogo, subastas, inventario.
- **T10 — Roadmap y Progreso:** XP, niveles, vidas, ranking P90/P10.
- **T11 — Social y Notificaciones:** chat, notificaciones, moderación.
- **T12 — Backoffice (nosotros):** administración de plataforma, configuración global, gestión del proveedor LLM, reportes docentes, panel del profesor, métricas, exportación y alertas.

### Concepto clave: curso-cohorte
La **cohorte** es la instancia concreta de un curso en un período académico (ej. "Programación I, comisión 2, 4to cuatrimestre 2026"). Es el **contexto compartido** de casi toda la plataforma: el ranking es por cohorte, las recompensas solo se usan en la cohorte donde se obtuvieron, la calibración es por curso. Es la clave `course_id` que viaja en cada operación y contra la que se acotan las consultas. El dueño de la cohorte es el **T02**.

---

## 2. Rol del Backoffice y stack técnico

### Qué es el Backoffice
El **Backoffice (T12)** es un **"consumidor puro"**: no tiene dominio propio. Todo lo que muestra pertenece a otros temas. Sus tareas (asignadas por el docente):

1. **Administración de plataforma** (operativa de ADMIN).
2. **Registro de parámetros PAR-01 a PAR-24** (configuración global de la economía).
3. **Gestión del proveedor LLM, exclusiva de ADMIN**.
4. **Contratos de lectura con los seis temas** que le proveen datos (02, 04, 05, 07, 08, 10).
5. **Reportes docentes**.
6. **Exportación de datos**.
7. **Panel del profesor con indicador de alumno en riesgo**.
8. **Frescura máxima de 15 minutos** en los datos.
9. **KPIs con CSAT de 5 estrellas**.
10. **Alertas configurables**.
11. **Sin comparación entre docentes**.

> Del documento oficial: *"Es consumidor puro: sin contratos de lectura acordados en el sprint 1 no tiene nada demostrable."*

### Stack tecnológico
- Java 21 (LTS) · Spring Boot 3 · Maven (multi-módulo).
- PostgreSQL (una base por servicio) · JPA/Hibernate · Flyway.
- Kafka (eventos) + patrón Outbox.
- Eureka (service discovery) · Spring Cloud Config Server.
- API Gateway de plataforma (Spring Cloud Gateway, pertenece al T01).
- Spring Security + JWT + 2FA (lo provee T01; nosotros lo consumimos).
- Documentación: OpenAPI/Swagger (springdoc).
- Pruebas: JUnit 5, Mockito, Testcontainers, Spring Cloud Contract (opcional).

### Nuestros 2 microservicios propietarios
1. **Administration & Configuration Service** — configuración global (PAR-01..24) + gestión del proveedor LLM (exclusiva de ADMIN). Base: `administration_db`.
2. **Reporting & Analytics Service** — reportes docentes, panel del profesor, métricas, exportación y alertas. Base: `reporting_db`.

Consumimos: identidad/auth/roles/2FA/auditoría/retención (T01), cohorte/matrícula (T02). Leemos: Temas 02/04/05/07/08/10.

---

## 3. Requerimientos Funcionales (RF)

### 3.1 Configuración global (Administration & Configuration)
- **RF-CFG-01:** el ADMIN administra configuraciones globales.
- **RF-CFG-04:** los parámetros de economía (PAR) son globales y solo los administra el ADMIN.
- **RF-CFG-05:** separación de ámbitos: el PROFESOR no puede modificar parámetros globales.
- **RF-CFG-06:** los cambios de parámetros aplican **solo hacia adelante**; nunca se recalculan XP/monedas históricos.
- **PAR-01 a PAR-24:** catálogo global (el PRD lista PAR-01..18; el registro es genérico y extensible). Ejemplos: PAR-01 XP por dificultad (100/250/500), PAR-06 precio de una vida (300), PAR-14 tolerancia de calibración, PAR-16 retención (5 años).

### 3.2 Gestión del proveedor LLM (exclusiva de ADMIN)
> **Aclaración clave:** los **proveedores de LLM son empresas externas** (OpenAI, Anthropic, etc.). El Backoffice **no es proveedor ni invoca a los modelos**: solo **administra la configuración** (qué proveedores están habilitados y qué modelo se usa para cada función). Quien **utiliza** los modelos es el **Tema 07 (Evaluación LLM)**, que consume esa configuración.

- **RF-IA-ADM-01:** alta/sustitución/baja de proveedores y modelos de LLM, exclusiva de ADMIN y auditada (RF-IA-35).
- **RF-IA-ADM-02:** asignación modelo ↔ función (tutor, evaluador, moderador, generador, RAG), configuración global (RF-IA-23/24).
- **RF-IA-ADM-03:** el evaluador de uso de IA tiene **un único modelo activo** (RF-IA-25).
- **RF-IA-ADM-04:** cambio de modelo evaluador sujeto a calibración (RF-IA-28).
- **RF-IA-ADM-05:** golden set base a nivel plataforma + calibración dentro de tolerancia (RF-IA-30/31).
- **RF-IA-ADM-06:** detección de deriva con re-calibración y alerta (RF-IA-32).
- **RF-IA-ADM-07:** trazabilidad de cohortes evaluadas con más de un modelo (RF-IA-33).
- El **T07 (Evaluación LLM)** consume esta configuración; nosotros solo la administramos.

### 3.3 Reportes docentes, métricas, exportación y alertas (Reporting & Analytics)
- **RF-RPT-01:** reportes docentes — el PROFESOR consulta sus cohortes; el ADMIN el consolidado de plataforma.
- **RF-RPT-02:** panel de métricas de curso — satisfacción (KPIs CSAT 5 estrellas, encuestas agregadas y anónimas), engagement, aprobación/abandono.
- **RF-RPT-03:** panel del profesor con **indicador de alumno en riesgo** (para más adelante).
- **RF-RPT-04:** exportación de datos (resúmenes y reportes CSV/PDF).
- **RF-RPT-05:** alertas configurables (para más adelante).
- **RF-RPT-06:** **frescura máxima de 15 minutos** en los datos de lectura.
- **RF-RPT-07:** **sin comparación entre docentes**.
- **RF-RPT-08:** no exposición de datos fuera de ámbito.
- **RF-RPT-09:** encuestas solo como **agregados anónimos** (RF-ENC-04/12), sin reconstruir autor ↔ respuesta.
- **RF-RPT-10:** **contratos de lectura** con los seis temas (02/04/05/07/08/10) — dependencia crítica del sprint 1.

### 3.4 Consumido del T01 (no se implementa)
Identidad, auth, 2FA, roles/permisos, sesión, **auditoría**, retención y el **API Gateway** pertenecen al T01. El Backoffice los consume para operar y autorizar sus endpoints. Principio: **validar ≠ autorizar** — el gateway valida el token; la autorización la decide el servicio dueño de la regla.

### 3.5 Fuera del alcance del Backoffice (otros temas)
Cursos y Matrícula (T02) · Motor de Desafíos (T03) · Teóricos y Encuestas (T04) · Desafíos Prácticos (T05) · Sandbox (T06) · Evaluación LLM (T07) · Banco (T08) · Mercado (T09) · Roadmap y Progreso (T10) · Social y Notificaciones (T11). El Backoffice los **lee** (02/04/05/07/08/10) para reportes y métricas, sin implementarlos.

---

## 4. Requerimientos No Funcionales (RNF)

- **Seguridad:** toda operación administrativa autenticada; autorización por rol y recurso.
- **Autenticación:** usuario + contraseña + **2FA obligatorio** (lo provee T01).
- **Autorización en dos niveles:** en el gateway (valida el token) y en el microservicio propietario (revalida identidad, rol, permisos y alcance). El gateway no es frontera suficiente.
- **Aislamiento de datos:** Database per Service (cada microservicio dueño de su base; sin FKs entre bases; relaciones por IDs).
- **Regla no negociable:** toda llamada síncrona entre microservicios **pasa por el gateway** (no hay comunicación directa); lo asincrónico viaja por **Kafka**.
- **Escalabilidad:** soportar 120 sesiones concurrentes (objetivo del PRD).
- **Resiliencia:** timeouts, retry con backoff, circuit breaker; operaciones asíncronas reintentables sin duplicar efectos.
- **Consistencia/idempotencia:** operaciones críticas idempotentes; consumidores toleran duplicados (idempotencia por `event_id`).
- **Trazabilidad:** correlation ID propagado en todo el recorrido (Gateway → Servicio → Evento → Consumidor).
- **Auditoría:** separada de los logs técnicos; la persiste T01, el Backoffice emite los eventos.
- **Borrado lógico:** no existe hard delete en producción académica.
- **Retención:** 5 años configurable, sin purga automática, decisión de ADMIN (consumida de T01).
- **Privacidad:** encuestas anónimas por diseño (sin vínculo autor ↔ respuesta).
- **Observabilidad:** logs estructurados, health checks, métricas.
- **Configuración:** secretos fuera del repositorio (variables de entorno / secret manager).
- **API:** REST documentada con OpenAPI/Swagger; versionado de API.
- **Fiabilidad de eventos:** Outbox + at-least-once + idempotencia.
- **Rate limiting y 429:** límites en el gateway; respuesta 429 con `Retry-After`; Idempotency-Key en operaciones administrativas críticas; el front aplica single-flight (una sola petición en vuelo).
- **Frescura:** datos de lectura del Backoffice con frescura máxima de 15 minutos.
- **Sin comparación entre docentes** en reportes.

---

## 5. Arquitectura

### Diagrama conceptual
```
Frontend (por definir) → API Gateway de PLATAFORMA (T01)
                              ├── Administration & Configuration Service → administration_db
                              └── Reporting & Analytics Service → reporting_db

Consume: T01 (identidad/auth/roles/auditoría) · T02 (cohorte/matrícula)
Lectura: 02/04/05/07/08/10 (contratos de lectura)

Infra: Eureka (discovery) · Config Server · Kafka (eventos) · PostgreSQL por servicio
```

### Patrones de diseño aplicados
- **Clean Architecture** por capas (`api → application → domain ← infrastructure`).
- **CQRS** (command/query) en la capa de aplicación.
- **Command** (casos de uso como objetos), **Specification** (reglas compuestas), **Adapter** (un cliente por tema para los contratos de lectura), **Null Object** (fallback), **Observer + Outbox** (eventos), **Unit of Work** (transacción + outbox), **Idempotency Key** (operaciones críticas).

## 5.1 Multitenancy y Row-Level Security (RLS)

**Decisión de la propuesta:** multitenancy **lógico** (tenant = curso-cohorte, `course_id`) + **RLS de PostgreSQL** como refuerzo.

**Por qué elegimos multitenancy lógico:**
- El documento oficial define el **curso-cohorte como el contexto compartido** de la plataforma: es la clave que viaja en cada operación y contra la que se acota cada consulta → tenant = `course_id`.
- Los cursos son **muchos y chicos**: crear una base o esquema por tenant multiplica la infraestructura sin beneficio real.
- El PRD no exige aislamiento físico por tenant → multitenancy lógico es barato y simple (un esquema por servicio, migraciones simples).

**Dónde aplica en el Backoffice:**
- **Administration & Configuration** (PAR-01..24, proveedores, evaluador, golden set): **global a propósito** (no tenant-scoped) — la economía debe valer igual en todos los cursos.
- **Reporting & Analytics** (métricas, reportes docentes, panel): **sí tenant-scoped por `course_id`** — read models acotados + pertenencia del actor.

**TenantContext:** componente que determina por operación el alcance del usuario: JWT → identidad/rol → `course_id` solicitado → validación de pertenencia (matrícula T02) → TenantContext autorizado. **Nunca confía en el `course_id` del request**; setea `app.current_course` en la sesión de la base (para RLS).

**RLS (Row-Level Security) como refuerzo:**
- Política por tabla tenant-scoped: `USING (course_id = current_setting('app.current_course')::uuid)`.
- La base **no devuelve filas de otros tenants** aunque falte el filtro en la query → defensa en profundidad (no depende de "acordarse" del `WHERE`).
- **RLS no reemplaza la autorización** (*validar ≠ autorizar*): filtra filas dentro de un tenant ya autorizado; la pertenencia a la cohorte se valida en la aplicación (T02).

**Convención de plataforma (para coordinar con otros equipos):** tenant = `course_id` · clave de sesión común `app.current_course` seteada por el TenantContext desde el contexto validado · RLS en tablas tenant-scoped de cada servicio (cada equipo sobre su propia base) · RLS complementa, no reemplaza, la autorización.

**Caso de prueba multitenancy (obligatorio):** PROFESOR A → cohorte A → 200 · PROFESOR A → cohorte B → 403.

**Caso ADMIN (vistas entre cursos):** el ADMIN tiene dos alcances sobre el reporting — **puntual** (`app.current_course = course_id`, mismo camino que un PROFESOR) y **global** (`app.current_course = 'ALL'`, centinela que habilita el panel general y las comparativas entre cursos). RLS **nunca se desactiva** (sin `BYPASSRLS`): la política incluye el centinela (`course_id = current_setting(...)::uuid OR current_setting(...) = 'ALL'`). **Quién puede setear 'ALL' lo decide la aplicación** (rol ADMIN validado en el token, o permiso explícito `REPORTS_VIEW_ALL`), **nunca el request** — un PROFESOR que intente alcance ALL recibe 403. Las lecturas globales se **auditan**. Pruebas: ADMIN → panel global → 200 · ADMIN → un curso puntual → 200 · PROFESOR → intento ALL → 403.

**Alternativas descartadas:** base por tenant (inviable, muchos cursos chicos) · esquema por tenant (sobreingeniería) · solo filtro en la app sin RLS (riesgo de fuga por olvido del filtro).

## 5.2 Mensajería híbrida (Kafka + caché con TTL)

**Decisión de la propuesta:** patrón **híbrido** de comunicación — REST por el gateway para lo síncrono + **Kafka** para eventos de configuración, con **caché local con TTL 10 min** en los consumidores. **RabbitMQ** queda documentado como alternativa.

**Por qué un patrón híbrido y por qué Kafka:**
- Hay dos necesidades distintas: **síncrona** (consultas/operaciones que requieren respuesta inmediata, por REST vía gateway) y **asíncrona** (avisar cambios de configuración global, por eventos). La regla: *REST responde; los eventos avisan*.
- **Kafka es la decisión de plataforma**: los grupos de **Notificaciones y Banco** también lo usan → un único broker para toda la plataforma simplifica integración y contratos.
- Aporta **replay/histórico** (retención configurable, re-leer topics) y **orden por partición** (`courseId`/`key`); las **particiones + consumer groups** permiten escalar y tolerar caídas (offset persistente).
- **RabbitMQ (alternativa)** es más liviano y bueno para *work-queues*, pero **no es la decisión de la plataforma**; el patrón híbrido (REST + eventos + caché TTL + Outbox) es idéntico.
- Los **read models de Reporting se reconstruyen vía contratos de lectura REST** desde los dominios dueños (no dependen del replay del broker, aunque Kafka lo ofrece).
- Es **infraestructura compartida** → decisión **coordinada con los demás equipos y validada con la cátedra**.

**Cómo fluye un cambio de configuración:**
1. El ADMIN cambia un PAR → el Backoffice persiste la nueva versión **+ Outbox** (misma transacción) y responde 200 sin esperar la propagación.
2. Publica `GlobalConfigurationChanged` en el **topic** `administration.events` → cada consumidor tiene su **consumer group**.
3. Cada consumidor (Temas 03/05/08/10) mantiene una **caché local con TTL de 10 min** (versión + valor). El evento **invalida/actualiza** la caché antes del vencimiento.
4. **Idempotencia por `event_id` y por `version`**: el consumidor descarta eventos con `versión ≤ local`.
5. **Resiliencia:** si el evento no llega, el TTL actúa como respaldo (a lo sumo 10 min con un valor anterior); si el Backoffice está caído, el consumidor sirve el último valor conocido.

**Contrato mínimo del evento:** `{eventId, parameterKey, version, value, validFrom, timestamp}` — identifica el cambio, no transporta lógica; la versión descarta duplicados/antiguos (RF-CFG-06: los cambios rigen hacia adelante).

**Topics del Backoffice:** `administration.events` (GlobalConfigurationChanged, ModelProviderChanged), `audit.events`, `retention.events`, `identity.events` (publica/consume) · `course.events`, `gamification.events`, `ranking.events`, `survey.events` (consume para reporting).

**Relación con multitenancy/RLS:** son **independientes** y conviven — la mensajería define *cómo viajan los cambios*; el multitenancy define *cómo se separan los datos*. Ambos forman parte de la propuesta.

## 5.3 Frontend del BackOffice (Caso A: Angular SSR + Nginx + BFF)

**Decisión:** el frontend sigue el **Caso A** de la materia Front (apps Angular SSR independientes en multirepos, integradas por **Nginx**) con **BFF por experiencia** — el **BFF de BackOffice es del equipo BackOffice**. La app, el BFF y Nginx son de la materia **Front** y **no suman microservicios de dominio** (el backend sigue con 2 servicios propietarios).

**Arquitectura:** `Navegador → Nginx (plataforma) → /backoffice (SSR) · /api/* → BFF → API Gateway (T01) → microservicios`.

**Nginx (roles de la teoría de Front):**
- **Servidor web**: sirve el Angular compilado (`dist/` → `nginx:alpine`).
- **Reverse proxy**: rutea `/api/*` al BFF.
- **Deep-links**: fallback `try_files $uri $uri/ /backoffice/index.html` (el index de esa app).
- **Cache/seguridad**: assets con hash inmutables, index no-cache, CSP/HSTS.

**Build y despliegue (alineado a la consigna de Front — Arquitectura y Despliegue U1):**
- **Docker en 2 etapas**: `node:20` compila → `nginx:alpine` sirve (sin Node en la imagen final).
- **`envsubst`**: el `nginx.conf` es una plantilla con `${BFF_URL}` → **la misma imagen sirve staging y producción** sin recompilar.
- **Load balancing**: `upstream` con los algoritmos de Nginx — **Round Robin** (por defecto), **Weighted** (`weight=3`), **Least Connections** (`least_conn`, carga despareja) e **IP Hash** (`ip_hash`, sticky sessions). Para el TP (~450 usuarios, mitad activos) alcanza **1 instancia**.
- **Estrategias evaluadas (las seis de la consigna):** **Rolling Update + Feature Flags** (base) · **Blue-Green** (alternativa si se exige cero downtime) · **Canary, A/B Testing y Shadow descartados** (exigen balanceo por porcentaje/monitoreo en tiempo real, o duplicar tráfico sin duplicar efectos — no se justifican en el TP).
- **Herramientas:** **CI/CD GitHub Actions** (build 2 etapas → tests → push imagen → deploy compose) · **Secretos** con variables de entorno (`envsubst`, nunca credenciales en el repo) · **Infraestructura inmutable** con imagen por release (Docker Compose; Terraform/Ansible si sobra tiempo) · **Monitoreo/rollback** con healthchecks `service_healthy`, logs de Nginx y tags por versión (Prometheus/Grafana opcional).

**Con quién se conecta (vía BFF → Gateway T01):**
- **Identity (T01)**: login, sesión (cookie httpOnly), roles, 401 → login.
- **Administration & Configuration** (nuestro): PAR-01..24, proveedores LLM, evaluador, golden set.
- **Reporting & Analytics** (nuestro): panel, reportes docentes, métricas/CSAT, export, alertas.
- **Cursos / Matrícula (T02)**: listar cursos (selector de tenant) y validar pertenencia.
- **Lecturas 02/04/05/07/08/10**: solo si el panel lo requiere.

**Reglas clave:** el front **no habla con Kafka ni con la base** (los eventos de configuración van a consumidores backend; el front lee los PAR vía BFF → Administration). El **multitenancy lo resuelve el BFF** (selector de alcance: curso puntual o **ALL** para ADMIN) seteando `app.current_course` desde la sesión validada — el front nunca manda un `course_id` suelto.

---

## 6. Integración con el resto de los microservicios (entre equipos)

### Qué le damos a los compañeros
- La **configuración de PAR** (evento `GlobalConfigurationChanged`) que los Temas 03/05/08/10 aplican (en vez de hardcodear los valores).
- La **gestión de proveedores LLM** (evento `ModelProviderChanged`) que el T07 consume.

### Qué necesitamos de ellos
- Los **contratos de lectura** de los Temas 02/04/05/07/08/10 (eventos/APIs por el gateway) — dependencia crítica del sprint 1.
- La **matrícula** (T02) para autorizar reportes por cohorte.
- La **autorización** (T01) para operar.

### Contratos de lectura por tema
| Tema | Datos que provee al Backoffice | Mecanismo |
|---|---|---|
| 02 Cursos/Matrícula | cohorte, matrícula, pertenencia docente | evento + API (gateway) |
| 04 Teóricos/Encuestas | agregados de encuestas (anónimos) | evento |
| 05 Desafíos Prácticos | entregas, resultados | evento |
| 07 Evaluación LLM | estado de calibración/deriva | evento |
| 08 Banco | saldos, movimientos | evento |
| 10 Roadmap y Progreso | progreso, XP, niveles | evento |

### Convención de eventos (envelope estándar)
```json
{
  "eventId": "uuid",
  "eventType": "GlobalConfigurationChanged",
  "occurredAt": "2026-08-28T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "source": "administration-service",
  "payload": {}
}
```

---

## 7. Planificación (backlog general · 14 historias)

- **Estructura:** **2 temas estratégicos → 5 épicas → 14 historias de usuario**. Estimación: **SP (Fibonacci)** por historia y **horas** por tarea (la capacidad real la define el Excel del equipo).
- **Must (43 SP):** Parámetros Globales (US-01/02) · Administración de la Plataforma (US-03) · Modelos LLM y Golden Set (US-04/05/06/07) · Contratos de Lectura e Ingesta (US-08/10).
- **Should (US-11..14):** Panel docente con riesgo · Tablero de KPIs con anonimato · Umbrales y alertas.
- **Could (US-09):** Exportación de reportes.

Regla del documento: "Para más adelante" se diseña ahora y se implementa después; "Podría ser" es un extra que vale menos que un núcleo terminado. Detalle completo: `plan/sprint0/uh/` y `plan/sprint0/tareas.md`.

---

## 8. Analogías para entender lo técnico

- **API Gateway** = el portero/guardia del edificio: todo entra por la misma puerta, valida la credencial y reparte a la oficina correcta.
- **Microservicio** = un departamento con su tarea: cada uno hace lo suyo y se coordina por mensajes.
- **Kafka** = el archivo/registro central de avisos: cada consumidor lee desde donde quedó (offset) y puede re-leer lo publicado (replay).
- **Outbox** = el cartero anota el aviso en su libreta antes de salir: si el correo se cae, el aviso no se pierde.
- **Idempotencia** = si te llega el mismo aviso dos veces, respondés una sola vez.
- **Database per Service** = cada equipo tiene su propio cuaderno; nadie escribe en el cuaderno del vecino.
- **Consumidor puro** = el Backoffice es un **tablero de control**: no fabrica los datos, solo administra la configuración y muestra/lee lo que producen los demás.
- **Validar ≠ autorizar** = el portero comprueba que la credencial sea válida, pero decidir si podés entrar a esa oficina lo decide la oficina dueña.
- **Frescura ≤ 15 min** = los números del tablero pueden tener hasta 15 minutos de atraso.

---

## 9. Argumentos para la defensa

1. **Alcance correcto:** seguimos la asignación oficial (Tema 12): consumidor puro, sin dominios ajenos.
2. **2 servicios propietarios** (Administration & Configuration, Reporting & Analytics) y el resto se consume/lee.
3. **Contratos de lectura** = la dependencia más riesgosa; se acuerdan en el sprint 1.
4. **Proveedor LLM exclusivo de ADMIN** con golden set + calibración + deriva (RF-IA-35/30/31/32).
5. **PAR-01..24** con registro genérico y cambios solo hacia adelante (RF-CFG-06).
6. **Integración correcta:** sync por el gateway (regla no negociable), asíncrono por Kafka con Outbox + idempotencia.
7. **Seguridad:** autorización en 2 niveles, 2FA (consumida de T01), rate limiting + 429, Idempotency-Key.
8. **Privacidad:** encuestas solo agregados anónimos; sin comparación entre docentes.
9. **Best practices:** Clean Architecture, CQRS, patrones, OpenAPI, Flyway, Testcontainers, observabilidad.