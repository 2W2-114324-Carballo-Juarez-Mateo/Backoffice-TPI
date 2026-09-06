# Sprint 0 — BackOffice (Tema 12)

> **Entregable:** un único PDF por grupo (Propuesta de Sprint 0 + DoD + capacidad + épicas + historias de usuario). Este `.md` es la **fuente** desde la que se genera el PDF.

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
- [ ] **1. Propuesta de Sprint 0 + tareas iniciales**
- [ ] **3. Capacidad del equipo** (Excel 2 hojas + justificación)
- [ ] **4. Primeras Épicas**
- [ ] **5. Primeras Historias de Usuario**