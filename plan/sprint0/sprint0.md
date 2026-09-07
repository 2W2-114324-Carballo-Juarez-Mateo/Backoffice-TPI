# Sprint 0 — BackOffice (Tema 12)

> **Entregable:** un único PDF por grupo (Propuesta de Sprint 0 + DoD + capacidad + épicas + historias de usuario). Este `.md` es la **fuente** desde la que se genera el PDF.

## 1. Propuesta de Sprint 0

### 1.1 Objetivo
Preparar las **bases de trabajo** del equipo para todo el proyecto: repositorios, entorno, estándares, contratos y backlog inicial. **No se implementan funcionalidades** en este sprint; sí se deja todo listo para que el Sprint 1 arranque con el **backend funcional** y un flujo demostrable.

### 1.2 Bases de trabajo (normas del equipo)
- **Tecnologías**: Java 21 + Spring Boot 3 + Maven · PostgreSQL · **Kafka** (Outbox + idempotencia + caché TTL) · Eureka + Config Server · API Gateway de plataforma (T01) · GitHub Actions (CI) · Testcontainers · OpenAPI/Swagger.
- **Git y revisión**: ramas `feature/*` + **Pull Request con revisión de ≥1 compañero** · mensajes convencionales (`feat:`, `fix:`, `docs:`) · `main` protegido (nada se mergea sin PR).
- **Calidad**: build Maven verde · **Clean Architecture** (sin reglas de negocio en controllers) · tests unitarios + integración (Testcontainers) · **sin secretos en el repo** (config por variables/`envsubst`).
- **Documentación**: `sdd/` + sitio **sincronizados** ante cualquier cambio · trazabilidad **`RF-XXX`**.
- **Contratos**: envelope de eventos estándar y naming de topics **acordados con los demás equipos** (T01/03/05/08/10) antes de implementar consumos.
- **Seguimiento**: todo el trabajo en **Taiga** (épicas → historias → tareas) · comunicación por el canal del equipo · revisión periódica del avance.

### 1.3 Tareas iniciales del Sprint 0
1. **Repos y git**: estructura confirmada en `Backoffice-TPI`, protección de `main`, CI (GitHub Actions) verde desde el arranque.
2. **Bootstrap de los 2 servicios**: `administration-service` y `reporting-service` (Spring Boot, PostgreSQL, Flyway, perfiles por entorno).
3. **Entorno compartido**: `docker-compose` completo (Kafka, PostgreSQL, Eureka, Config Server, Gateway simulado de T01).
4. **Contrato de eventos**: envelope estándar + topics (`administration.events`, etc.) coordinado con los equipos consumidores.
5. **DoD**: definirla (sección 2 de este documento) y cargarla en Taiga.
6. **Backlog en Taiga**: cargar épicas y primeras historias de usuario.
7. **Planificación del Sprint 1**: seleccionar historias según la **capacidad efectiva** del equipo (sección 3).

### 1.4 Resultado esperado del Sprint 0
Repos y entorno levantados · DoD definida · backlog inicial en Taiga · contrato de eventos acordado · **Sprint 1 planificado** con las historias que la capacidad permite.

## 2. Definition of Done (DoD)

> **Sobre la adaptación:** esta DoD no es una definición genérica. Cada condición se ancla a la **tecnología** del proyecto (Java/Spring, PostgreSQL, Kafka, RLS, OpenAPI), a nuestros **controles de calidad** (Testcontainers, Outbox + idempotencia, revisión de PR) y a nuestras **entregas** (`sdd/` + sitio sincronizados, Taiga, trazabilidad `RF-XXX`). Sigue la estructura profesional en niveles (tarea · historia · sprint · release).

**Aplicación:** una **tarea**, **historia de usuario**, **sprint** o **release** está *terminada* cuando cumple todas las condiciones de su nivel (y los niveles inferiores).

### Nivel 0 · Tarea
- Cumple sus **criterios de aceptación propios** (los definidos por tarea en `sdd/`).
- **Build verde** (`mvn clean verify`) · tests de la tarea pasando.
- **Cobertura de tests ≥ 90%** (objetivo: se intenta lograr siempre en lo posible).
- Una tarea se cierra solo cuando su **historia** también alcanza el **Nivel 1**.

### Nivel 1 · Historia de Usuario
- Cumple el **requisito citado (`RF-XXX`)** y sus **criterios de aceptación**.
- **Build verde** (`mvn clean verify`) · sin warnings críticos · **Clean Architecture** (sin reglas de negocio en controllers).
- **Tests** unitarios de dominio/casos de uso + **integración con Testcontainers** (PostgreSQL + Kafka) donde publique/consuma eventos.
- **Cobertura de tests ≥ 90%** (objetivo: se intenta lograr siempre en lo posible).
- **Outbox + idempotencia** verificados (por `event_id` y `version`).
- **Autorización por rol** probada (ADMIN/PROFESOR → 200/403).
- **Alcance multitenancy** verificado: RLS filtra por `course_id`; `ALL` solo ADMIN y auditado.
- **Contrato OpenAPI/eventos** documentado y coordinado con consumidores.
- **PR revisada por ≥1 compañero** · sin secretos en el repo.
- **`sdd/` + sitio sincronizados**.

### Nivel 2 · Sprint
- **Todas sus historias cumplen el Nivel 1**.
- **Sin regresiones**: suite completa verde al cierre.
- **Taiga actualizado** (estados correctos · capacidad respetada).
- **Revisión/demo del sprint lista** (flujo demostrable).
- **Retrospectiva realizada** y próximas acciones registradas.

### Nivel 3 · Release
- **`main` con CI verde** y **tag/versión**.
- **Desplegable**: imagen/entorno listo (Docker Compose, `envsubst`, sin Node en producción).
- **Contratos estables** y coordinados con los demás equipos (eventos + API).
- **Documentación de la release** alineada (sitio + `sdd/`).

> El frontend (app Angular + BFF) se agregará como bloque propio cuando lo definamos; por ahora la DoD cubre el **backend funcional**.

---

## 4. Primeras Épicas (clasificadas por tema)

### T-A · Gobernanza y Configuración Institucional
> **Quién tiene poder de actuar sobre la plataforma y bajo qué reglas:** humanos con rol **ADMIN** (Épicas 1 y 2) y **modelos de IA habilitados** (Épica 3). Todas **escriben/deciden**, no solo muestran.

| Épica | Alcance | RF base | Prioridad |
|---|---|---|---|
| **Épica 1 — Parámetros Globales** | Reglas de economía y operativas (PAR-01..24) que define el ADMIN y aplican los Temas 03/05/08/10 | RF-CFG-04/06 | Must |
| **Épica 2 — Administración de la Plataforma** | Gestión de administradores y roles: quién puede operar | RF-CFG-01/05 · RF-ROL | Must |
| **Épica 3 — Modelos LLM y Golden Set** | Proveedores/modelos de IA, evaluador, calibración y deriva (exclusivo ADMIN) | RF-IA-ADM-01..07 | Must |

### T-B · Observabilidad y Soporte Académico
> **Muestra información en vez de gobernarla:** el **PROFESOR** solo consulta (no configura nada); el **ADMIN** ve el consolidado. Incluye el habilitador transversal de los contratos de lectura.

| Épica | Alcance | RF base | Prioridad |
|---|---|---|---|
| **Épica 4 — Contratos de Lectura** | Consumo de eventos/lecturas de los Temas 02/04/05/07/08/10 para construir los read models (habilitador de todo el tema) | RF-RPT-10 | Must |
| **Épica 5 — Observabilidad, Reportes y Panel de Riesgo** | Reportes docentes, panel de métricas, alumno en riesgo, export y alertas | RF-RPT-01/02/03/04/05 | Must / Should / Could |

**Futura:** Frontend BackOffice (app Angular + BFF) — a definir cuando se aborde la materia Front.

## 5. Primeras Historias de Usuario

Formato: *Como [rol], quiero [acción], para [beneficio]* + criterios de aceptación. Agrupadas por tema y épica.

### T-A · Gobernanza y Configuración Institucional

#### Épica 1 · Parámetros Globales (PAR-01..24)
> El catálogo de reglas que rigen toda la plataforma. El ADMIN las configura; los Temas 03/05/08/10 las aplican. **Propósito:** que ninguna regla numérica quede fija en el código de un microservicio.

**US-01** · Como **ADMIN**, quiero **crear y editar los parámetros globales (PAR-01..24)**, para definir la economía y las reglas operativas desde una sola consola.
- *Para qué importa:* evita valores hardcodeados; un cambio se propaga sin recompilar.
- Aceptación: **versionado** y cambios **solo hacia adelante** (RF-CFG-06) · idempotencia · evento `GlobalConfigurationChanged`.

**US-02** · Como **consumidor (Temas 03/05/08/10)**, quiero **recibir el cambio de parámetro**, para aplicar la configuración vigente sin hardcodear.
- *Para qué importa:* todos los cursos usan el mismo valor vigente → economía coherente.
- Aceptación: **Outbox** + idempotencia por `version` · **caché TTL 10 min** (respaldo si el evento no llega).

#### Épica 2 · Administración de la Plataforma
> Control de **quién puede operar**: alta/baja de administradores y roles. Protege al sistema (último admin) y deja rastro (auditoría).

**US-03** · Como **ADMIN**, quiero **dar de alta y de baja administradores**, para controlar quién opera la plataforma.
- *Para qué importa:* escalar el equipo de operación y retirar accesos sin quedar sin admins ni sin rastro.
- Aceptación: solo ADMIN · un admin **no puede auto-eliminarse** (RF-ROL-02) · **protección del último admin** (RF-ROL-05) · auditada (RF-AUD-03).

#### Épica 3 · Modelos LLM y Golden Set
> Gobernanza de los modelos de IA (evaluador): **qué proveedor/modelo se usa y que esté calibrado** antes de habilitarse. Exclusivo de ADMIN.

**US-04** · Como **ADMIN**, quiero **dar de alta, sustituir o dar de baja un proveedor o modelo de IA**, para decidir qué modelo se usa en cada función (ej. evaluador).
- Aceptación: exclusivo ADMIN · auditado (RF-IA-35) · evento `ModelProviderChanged`.

**US-05** · Como **ADMIN**, quiero **habilitar un modelo evaluador solo si pasa el golden set**, para garantizar la calidad antes de que se use.
- Aceptación: **calibración dentro de PAR-14** · modelo único activo · **deriva → alerta** (RF-IA-32).

### T-B · Observabilidad y Soporte Académico

#### Épica 4 · Contratos de Lectura
> Habilitador transversal del tema: el Reporting consume los datos de los demás equipos para poder mostrar algo. **Sin esto, no hay reportes.**

**US-06** · Como **Reporting**, quiero **consumir eventos/lecturas de los Temas 02/04/05/07/08/10**, para construir los read models.
- *Para qué importa:* es la fuente de datos de todo el tema T-B; se acuerda con los equipos al inicio.
- Aceptación: contratos **acordados con los equipos** · envelope estándar · adapter por tema (RF-RPT-10).

#### Épica 5 · Observabilidad, Reportes y Panel de Riesgo
> La épica que **muestra** en vez de gobernar: reportes y métricas. El **PROFESOR consulta solo su curso**; el **ADMIN** ve el consolidado.

**US-07** · Como **PROFESOR**, quiero **ver los reportes de mi curso-cohorte**, para evaluar el avance de mis alumnos.
- *Para qué importa:* toma de decisiones pedagógicas con datos reales de su cohorte.
- Aceptación: **solo su curso** (otro → 403, RLS) · panel con alumno en riesgo (RF-RPT-03).

**US-08** · Como **ADMIN**, quiero **ver el consolidado global de métricas (y por curso)**, para monitorear la plataforma completa.
- Aceptación: alcance **`ALL` solo ADMIN y auditado** · RLS por `course_id`.

**US-09** · Como **ADMIN/PROFESOR**, quiero **exportar reportes**, para usarlos fuera de la plataforma.
- Aceptación: export CSV/PDF generado por backend (RF-RPT-04 · Could).

---

## Secciones pendientes (se agregan en orden)
- [x] **1. Propuesta de Sprint 0 + tareas iniciales**
- [x] **2. Definition of Done (DoD)**
- [ ] **3. Capacidad del equipo** (Excel 2 hojas + justificación)
- [x] **4. Primeras Épicas**
- [x] **5. Primeras Historias de Usuario**