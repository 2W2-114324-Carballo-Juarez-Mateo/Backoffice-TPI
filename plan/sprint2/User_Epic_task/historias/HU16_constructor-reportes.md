# HU16 — Constructor de reportes (frontend)

> Épica: [Observabilidad, Reportes y Panel de Riesgo](../epicas/E05_reportes-panel-riesgo.md) · Taiga: por crear · Acción: **CREAR historia nueva** · Estado actual: Nueva (Sprint 2)

## Descripción
**Como** PROFESOR
**Quiero** una pantalla para elegir métricas, filtros, período, columnas y agrupación, ver el resultado y guardar la plantilla
**Para** armar mis reportes sin conocimientos técnicos

## Notas / Observaciones

- **Reglas de negocio:**
  - Solo ofrece las métricas del catálogo; las no disponibles se ven deshabilitadas con el motivo.
  - No existe la opción de agrupar por docente.
  - Guardar una plantilla valida los datos y da feedback accesible.
- **Validaciones:**
  - No se puede ejecutar sin curso, métrica y período.
  - Un 403 muestra un mensaje claro y ningún dato.
- **Datos obligatorios:** curso, métricas, período, columnas, agrupación
- **Performance:** la pantalla no bloquea mientras se ejecuta el reporte.
- **Seguridad:** el frontend no decide permisos: muestra lo que el backend autoriza.
- **Accesibilidad:** cumple WCAG AA, se usa por teclado y no depende solo del color.
- **Otros:** es la fase 2 de los reportes dinámicos; si el tiempo no alcanza pasa al sprint siguiente.

## Criterios de Aceptación
- **CA1:** El PROFESOR arma, ejecuta y guarda una plantilla desde la pantalla.
- **CA2:** Las métricas no disponibles se muestran deshabilitadas con su motivo.
- **CA3:** Un curso ajeno muestra el 403 sin datos.
- **CA4:** Hay specs de crear, editar y correr una plantilla.

## BDD (mínimo 3 escenarios)

**Característica:** Constructor de reportes

**Escenario 1 — Armar y guardar**
- **Dado** que el PROFESOR eligió curso, métricas y período
- **Cuando** presiona "Guardar plantilla"
- **Entonces** la plantilla queda en su lista

**Escenario 2 — Métrica no disponible**
- **Dado** que T10 todavía no firmó su contrato
- **Cuando** el PROFESOR abre el catálogo
- **Entonces** la métrica de promoción aparece deshabilitada con el motivo

**Escenario 3 — Curso ajeno**
- **Dado** que el PROFESOR cambia el curso por uno ajeno
- **Cuando** ejecuta el reporte
- **Entonces** ve el mensaje de acceso denegado y ninguna fila

## Prototipo (Mock API / Swagger)
- Pantalla `reports/builder` (frontend)
- Consume GET /api/backoffice/reports/metrics y POST /api/backoffice/reports/run

## Estimación / Prioridad
- **Puntos (Fibonacci):** 3
- **Prioridad (MoSCoW):** Should

## Dependencias / Impactos
- **Servicios / APIs:** frontend, reporting-service
- **Módulos afectados:** constructor de reportes, plantillas
- **Otros equipos:** ninguno
- **Datos / migraciones:** ninguno
- **Riesgos:** depende del motor y del catálogo → se desarrolla contra el contrato congelado

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [FRONTEND] - Implementar constructor de reportes con guardado de plantillas (WCAG AA) | Damián Baigorria | Frontend, Diseño / UX-UI | CP5 | — |
| Nueva | [G06] - [TEST] - Desarrollar specs del constructor (crear, editar, correr plantilla y 403) | Bruno Gianoli | Testing, Frontend | CP5 | — |
| Nueva | [G06] - [DOCUMENTACION] - Documentar en la wiki la vista del constructor y el catálogo de métricas | Ana Ducart | Documentación | CP5 | — |
| Nueva | [G06] - [REVISION] - Peer review del constructor de reportes | Regina Cerasulo | Testing | CP5 | — |

### Texto para copiar en Taiga

```text
[G06] - [FRONTEND] - Implementar constructor de reportes con guardado de plantillas (WCAG AA)
[G06] - [TEST] - Desarrollar specs del constructor (crear, editar, correr plantilla y 403)
[G06] - [DOCUMENTACION] - Documentar en la wiki la vista del constructor y el catálogo de métricas
[G06] - [REVISION] - Peer review del constructor de reportes
```
