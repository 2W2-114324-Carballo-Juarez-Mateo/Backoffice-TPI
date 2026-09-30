# HU15 — Reportes docentes dinámicos (motor y ejecución)

> Épica: [Observabilidad, Reportes y Panel de Riesgo](../epicas/E05_reportes-panel-riesgo.md) · Taiga: por crear · Acción: **CREAR historia nueva** · Estado actual: Nueva (Sprint 2)

## Descripción
**Como** PROFESOR
**Quiero** armar un reporte eligiendo métricas, filtros, período, columnas y agrupación, y guardar la configuración como plantilla
**Para** consultar la información de mis cursos que necesito sin pedir un reporte fijo

## Notas / Observaciones

- **Reglas de negocio:**
  - Las métricas y las dimensiones salen de una lista blanca; no se aceptan expresiones libres ni SQL concatenado.
  - Ninguna dimensión agrupa por docente (RF-RPT-07).
  - El profesor solo consulta sus cursos; el ADMIN puede consultar todos.
  - Las métricas de encuesta solo se ofrecen agregadas, con PAR-18 y curso cerrado.
  - Toda respuesta informa la frescura de los datos (máximo 15 minutos).
- **Validaciones:**
  - Una métrica o dimensión desconocida responde 400.
  - El período máximo y el tamaño de página tienen un tope.
  - Un curso ajeno responde 403.
- **Datos obligatorios:** métricas, dimensiones, filtros, período, curso
- **Performance:** la ejecución responde en menos de 3 segundos con los datos de demo.
- **Seguridad:** se ejecuta dentro del resolvedor de alcance y con RLS por curso; las plantillas solo las ve su dueño.
- **Accesibilidad:** la respuesta incluye los nombres de columna para tablas accesibles.
- **Otros:** las métricas de promoción, abandono, CSAT y entregas dependen de los contratos con T10, T02 y T05 y se ofrecen como "no disponible" hasta entonces.

## Criterios de Aceptación
- **CA1:** El PROFESOR ejecuta un reporte con métricas de la lista blanca sobre uno de sus cursos.
- **CA2:** Pedir una métrica desconocida o un curso ajeno responde 400 o 403 respectivamente.
- **CA3:** El catálogo informa qué métricas están disponibles y cuáles esperan un contrato.
- **CA4:** El reporte no permite agrupar por docente.
- **CA5:** Las plantillas se guardan, se listan y se marcan como favoritas solo para su dueño.

## BDD (mínimo 3 escenarios)

**Característica:** Reportes docentes dinámicos

**Escenario 1 — Reporte sobre mi curso**
- **Dado** que un PROFESOR tiene asignado el curso A
- **Cuando** ejecuta un reporte de aprobación por semana del curso A
- **Entonces** recibe la tabla con su frescura

**Escenario 2 — Curso ajeno**
- **Dado** que el PROFESOR solo tiene el curso A
- **Cuando** pide un reporte del curso B
- **Entonces** responde 403 y no consulta la base de datos

**Escenario 3 — Métrica fuera de la lista**
- **Dado** que el PROFESOR envía una métrica inventada
- **Cuando** ejecuta el reporte
- **Entonces** responde 400 con un mensaje claro

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/reports/metrics → catálogo de métricas
- POST /api/backoffice/reports/run → ejecutar un reporte (con plantilla o configuración)
- CRUD /api/backoffice/reports/templates → plantillas y favoritas

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service
- **Módulos afectados:** catálogo de métricas, motor de consultas, plantillas, acceso por rol y RLS
- **Otros equipos:** T02, T03, T05, T10 (fuentes)
- **Datos / migraciones:** V21 (plantillas)
- **Riesgos:** SQL dinámico → lista blanca por enumeración y parámetros; revisión de seguridad dedicada

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Implementar catálogo de métricas permitidas (lista blanca) con su disponibilidad | Joaquín Cortez | Backend, Análisis | CP3 | — |
| Nueva | [G06] - [BACKEND] - Implementar motor de consultas dinámicas con acceso por curso | Bruno Gianoli | Backend, Seguridad, Base de Datos | CP4 | — |
| Nueva | [G06] - [BACKEND] - Implementar plantillas y favoritas de reportes | Joaquín Cortez | Backend, Base de Datos | CP3 | Migración V21 |
| Nueva | [G06] - [BACKEND] - Implementar endpoint de ejecución de reportes (run) con plantilla o configuración | Bruno Gianoli | Backend, Seguridad | CP4 | — |
| Nueva | [G06] - [TEST] - Desarrollar tests del motor (RLS, anti-comparación, anonimato y lista blanca) | Máximo Cerquatti | Testing, Seguridad | CP4 | — |
| Nueva | [G06] - [DOCUMENTACION] - Documentar en OpenAPI métricas, plantillas y ejecución de reportes | Joaquín Cortez | Documentación | CP4 | — |
| Nueva | [G06] - [REVISION] - Peer review de seguridad del motor (RLS y lista blanca) | Valentina Maldonado | Testing, Seguridad | CP4 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar catálogo de métricas permitidas (lista blanca) con su disponibilidad
[G06] - [BACKEND] - Implementar motor de consultas dinámicas con acceso por curso
[G06] - [BACKEND] - Implementar plantillas y favoritas de reportes
[G06] - [BACKEND] - Implementar endpoint de ejecución de reportes (run) con plantilla o configuración
[G06] - [TEST] - Desarrollar tests del motor (RLS, anti-comparación, anonimato y lista blanca)
[G06] - [DOCUMENTACION] - Documentar en OpenAPI métricas, plantillas y ejecución de reportes
[G06] - [REVISION] - Peer review de seguridad del motor (RLS y lista blanca)
```
