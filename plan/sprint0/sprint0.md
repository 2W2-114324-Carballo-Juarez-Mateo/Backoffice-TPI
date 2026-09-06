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
- Una tarea se cierra solo cuando su **historia** también alcanza el **Nivel 1**.

### Nivel 1 · Historia de Usuario
- Cumple el **requisito citado (`RF-XXX`)** y sus **criterios de aceptación**.
- **Build verde** (`mvn clean verify`) · sin warnings críticos · **Clean Architecture** (sin reglas de negocio en controllers).
- **Tests** unitarios de dominio/casos de uso + **integración con Testcontainers** (PostgreSQL + Kafka) donde publique/consuma eventos.
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

## Secciones pendientes (se agregan en orden)
- [x] **1. Propuesta de Sprint 0 + tareas iniciales**
- [x] **2. Definition of Done (DoD)**
- [ ] **3. Capacidad del equipo** (Excel 2 hojas + justificación)
- [ ] **4. Primeras Épicas**
- [ ] **5. Primeras Historias de Usuario**