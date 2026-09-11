# Sprint 0 — Propuesta inicial de trabajo (BackOffice · Tema 12)

> **Entregable:** un único PDF por grupo con la propuesta de Sprint 0. Este documento define el **marco general** (no está atado a un Sprint puntual): propuesta, Definition of Done, épicas (en formato template) e historias de usuario (en `uh/`).

## 1. Propuesta de Sprint 0

**Objetivo:** preparar las **bases de trabajo** del equipo para todo el proyecto: repositorios, entorno, estándares, contratos y backlog inicial. No se implementan funcionalidades; se deja todo listo para arrancar con el backend funcional.

**Bases de trabajo (normas del equipo):**
- **Tecnologías:** Java 21 + Spring Boot 3 + Maven · PostgreSQL · **Kafka** (Outbox + idempotencia + caché TTL) · Eureka + Config Server · Gateway de plataforma (T01) · GitHub Actions (CI) · Testcontainers · OpenAPI.
- **Git y revisión:** ramas `feature/*` + **PR revisada por ≥1 compañero** · `main` protegido · mensajes convencionales.
- **Calidad:** build Maven verde · Clean Architecture · tests unitarios + integración (Testcontainers) · **sin secretos en el repo**.
- **Documentación:** `sdd/` + sitio sincronizados · trazabilidad `RF-XXX`.
- **Contratos:** envelope de eventos estándar y topics acordados con los demás equipos.
- **Seguimiento:** todo en **Taiga** (épicas → historias → tareas).

**Tareas iniciales del Sprint 0:**
1. Repos y git (estructura, `main` protegido, CI verde).
2. Bootstrap de `administration-service` y `reporting-service`.
3. Entorno compartido (`docker-compose`: Kafka, PostgreSQL, Eureka, Config Server, Gateway simulado).
4. Contrato de eventos (envelope estándar + topics).
5. DoD definida y cargada en Taiga.
6. Backlog inicial en Taiga (épicas + historias).
7. Planificación según capacidad efectiva.

**Resultado esperado:** repos y entorno levantados · DoD definida · backlog en Taiga · contratos acordados.

## 2. Definition of Done (DoD)

> **En simple:** una tarea, historia o entrega está *terminada* cuando cumple todas las condiciones de su nivel. La DoD está **adaptada** a nuestra forma de trabajo (no es una copia genérica): se ancla a nuestra tecnología (Java/Spring, PostgreSQL, Kafka, RLS, OpenAPI), a nuestros controles de calidad (Testcontainers, Outbox + idempotencia, revisión de PR) y a nuestras entregas (sdd + sitio, Taiga, trazabilidad RF).

### Nivel 0 · Tarea
- Cumple sus criterios de aceptación propios (los de cada tarea en `sdd/`).
- Build verde (`mvn clean verify`) y tests de la tarea pasando.
- Cobertura de tests ≥ 90% (objetivo, siempre que sea posible).
- Se cierra cuando su historia también alcanza el Nivel 1.

### Nivel 1 · Historia de Usuario
- Cumple el requisito citado (`RF-XXX`) y sus criterios de aceptación.
- Build verde, sin warnings críticos, Clean Architecture (sin reglas de negocio en controllers).
- Tests unitarios + integración (Testcontainers: PostgreSQL + Kafka) donde haya eventos.
- Cobertura de tests ≥ 90% (objetivo).
- Outbox + idempotencia verificados (por `event_id` y `version`).
- Autorización por rol probada (ADMIN/PROFESOR → 200/403).
- Multitenancy + RLS verificado: `ALL` solo ADMIN y auditado.
- Contrato OpenAPI/eventos documentado y coordinado.
- PR revisada por ≥1 compañero · sin secretos.
- `sdd/` + sitio sincronizados.

### Nivel 2 · Sprint
- Todas sus historias cumplen el Nivel 1.
- Sin regresiones (suite completa verde al cierre).
- Taiga actualizado (estados correctos · capacidad respetada).
- Revisión/demo lista · retrospectiva realizada.

### Nivel 3 · Release
- `main` con CI verde y tag/versión.
- Desplegable (imagen/entorno listo).
- Contratos estables y coordinados.
- Documentación alineada (sitio + sdd).

> El frontend (app Angular + BFF) se agrega como bloque propio cuando lo definamos; por ahora la DoD cubre el backend funcional.

## 3. Capacidad del equipo

_Pendiente de completar._ Excel de 2 hojas (`CAPACIDAD_SPRINT` + `RESUMEN`). Fórmula: `Capacidad efectiva = ((Días del Sprint − Ausencias) × Horas por día − Otras actividades) × % de dedicación`.

## 4. Épicas (formato template)

Cada épica está definida con el **template del equipo** (`templateEpicas.md`) en la carpeta `epicas/`.

### TH-01 · Gobernanza y Configuración Institucional
*Quién tiene poder de actuar y bajo qué reglas: humanos con rol ADMIN y modelos de IA habilitados. Todas escriben/deciden.*

- **[G06] Épica 01 · Parámetros Globales** → [epicas/EP-01.md](epicas/EP-01.md)
- **[G06] Épica 02 · Administración de la Plataforma** → [epicas/EP-02.md](epicas/EP-02.md)
- **[G06] Épica 03 · Modelos LLM y Golden Set** → [epicas/EP-03.md](epicas/EP-03.md)

### TH-02 · Observabilidad y Soporte Académico
*Muestra información en vez de gobernarla: el PROFESOR solo consulta, el ADMIN ve el consolidado.*

- **[G06] Épica 04 · Contratos de Lectura e Ingesta** → [epicas/EP-04.md](epicas/EP-04.md)
- **[G06] Épica 05 · Observabilidad, Reportes y Panel de Riesgo** → [epicas/EP-05.md](epicas/EP-05.md)

> **Futura:** TH-03 · Experiencia de Usuario (frontend Angular + BFF) — a definir.

## 5. Historias de Usuario (formato template)

Cada historia está en `uh/` con el **template del equipo** (`templateUH.md`): Descripción (Como/Quiero/Para) · Reglas de negocio · CA · BDD · Estimación · **Tareas asociadas**.

| Historia | En simple | Especificación |
|---|---|---|
| **US-01** | El admin cambia las reglas desde una pantalla; queda anotado y vale de ahora en adelante. | [uh/US-01.md](uh/US-01.md) |
| **US-02** | Al cambiar una regla, el sistema avisa a los demás sin perder el aviso. | [uh/US-02.md](uh/US-02.md) |
| **US-03** | El admin da y quita el rol de administrador, nunca sin responsables. | [uh/US-03.md](uh/US-03.md) |
| **US-04** | El admin registra proveedores y modelos de IA, sin exponer claves. | [uh/US-04.md](uh/US-04.md) |
| **US-05** | El admin activa y cambia el modelo de IA en uso; el sistema avisa a los servicios. | [uh/US-05.md](uh/US-05.md) |
| **US-06** | El sistema prueba un modelo contra respuestas de referencia y guarda el resultado. | [uh/US-06.md](uh/US-06.md) |
| **US-07** | Un modelo solo se usa si su error está dentro de lo permitido; si se desvía, se saca solo. | [uh/US-07.md](uh/US-07.md) |
| **US-08** | El sistema junta los datos de los otros temas y guarda cada uno una sola vez. | [uh/US-08.md](uh/US-08.md) |
| **US-09** | Se puede pedir un reporte en PDF/CSV y avisan cuando está listo. | [uh/US-09.md](uh/US-09.md) |
| **US-10** | El sistema avisa cuando un tema lleva demasiado tiempo sin enviar datos. | [uh/US-10.md](uh/US-10.md) |
| **US-11** | El sistema arma el resumen de cada comisión y calcula el riesgo de cada alumno. | [uh/US-11.md](uh/US-11.md) |
| **US-12** | El profesor ve el panel de sus comisiones y avisa si un alumno está en riesgo alto. | [uh/US-12.md](uh/US-12.md) |
| **US-13** | El admin ve el tablero de indicadores y las muestras muy chicas se ocultan. | [uh/US-13.md](uh/US-13.md) |
| **US-14** | El admin define cuándo un indicador está bajo y el sistema avisa; solo él lo ve. | [uh/US-14.md](uh/US-14.md) |

> **Todas las tareas** de las 14 historias (en simple, con **SP Fibonacci en la historia y horas en cada tarea**) están consolidadas en [tareas.md](tareas.md), listas para cargar en Taiga. Los **responsables** se asignan aparte. Las tareas de **Frontend** están en el bloque TH-03 (futura) y no computan en la capacidad de Back.

## 6. Matriz de trazabilidad (resumen)

| Épica | Historias | SP | Prioridad |
|---|---|---|---|
| EP-01 · Parámetros Globales | US-01, US-02 | 5+5 | Must |
| EP-02 · Administración de la Plataforma | US-03 | 5 | Must |
| EP-03 · Modelos LLM y Golden Set | US-04, US-05, US-06, US-07 | 5+5+5+5 | Must |
| EP-04 · Contratos de Lectura e Ingesta | US-08, US-10 | 5+3 | Must |
| EP-05 · Observabilidad, Reportes y Panel | US-09, US-11, US-12, US-13, US-14 | 5+5+5+5+3 | Could / Should / Should / Should / Should |

> **Backlog general:** las historias **Must** suman **43 SP**; la capacidad efectiva define cuántas se toman por Sprint.