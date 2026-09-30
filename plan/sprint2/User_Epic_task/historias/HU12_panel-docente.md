# HU12 — Panel del docente con RLS y alerta de riesgo

> Épica: [Observabilidad, Reportes y Panel de Riesgo](../epicas/E05_reportes-panel-riesgo.md) · Taiga: #24 · Acción: **Mover al Sprint 2 y actualizar la descripción** · Estado actual: Backlog

## Descripción
**Como** PROFESOR
**Quiero** ver el panel de mis comisiones con el semáforo de riesgo y que el sistema avise cuando un alumno está en riesgo alto
**Para** intervenir a tiempo, sin poder ver información de otras comisiones

## Notas / Observaciones

- **Reglas de negocio:**
  - El profesor solo ve sus propias comisiones, validadas con la pertenencia que informa T02.
  - Si T02 no responde, el profesor queda denegado (falla cerrada); el ADMIN puede operar.
  - El panel muestra el estado de riesgo calculado en HU11 (rojo, amarillo, verde).
  - Cuando un alumno pasa a rojo se publica `STUDENT_AT_HIGH_RISK` una sola vez, en `notifications.events`.
  - No se permite comparar a los profesores entre sí.
- **Validaciones:**
  - La comisión consultada debe pertenecer al profesor; si no, responde 403.
  - El aviso solo se emite en la transición a rojo.
- **Datos obligatorios:** curso-cohorte del alumno, estado de riesgo, factores de riesgo
- **Performance:** el panel carga en menos de 2 segundos.
- **Seguridad:** RLS por curso con `FORCE ROW LEVEL SECURITY`; comisiones ajenas responden 403; el mensaje lleva identificadores y no datos personales.
- **Accesibilidad:** panel usable por teclado, con colores y también texto (WCAG AA).
- **Otros:** el panel incluye la frescura de los datos (HU10).

## Criterios de Aceptación
- **CA1:** El profesor ve solo los alumnos de sus comisiones con su estado de riesgo.
- **CA2:** Si un alumno pasa a riesgo alto, se envía el aviso al sistema de notificaciones una sola vez.
- **CA3:** Si intenta consultar una comisión ajena, responde 403 y no muestra datos.
- **CA4:** La vista no permite comparar a docentes entre sí.

## BDD (mínimo 3 escenarios)

**Característica:** Panel docente con RLS y alerta de riesgo

**Escenario 1 — Alumno en riesgo alto**
- **Dado** que el profesor tiene asignada la comisión 2W2 y un alumno quedó en ROJO
- **Cuando** se actualiza el panel
- **Entonces** el alumno se muestra en riesgo alto y se envía el aviso de notificación

**Escenario 2 — Comisión ajena**
- **Dado** que el profesor solo tiene la comisión CURS-2W2
- **Cuando** intenta consultar CURS-2W3
- **Entonces** responde 403 y no muestra ningún dato

**Escenario 3 — Sin comparaciones**
- **Dado** que el profesor revisa el resumen de su comisión
- **Cuando** visualiza los resultados
- **Entonces** solo ve el promedio de su propia comisión, sin comparar contra otros docentes

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/reports/courses/{courseId}/teacher → panel del docente

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service, T02, T11
- **Módulos afectados:** panel, acceso por rol y RLS, aviso de riesgo
- **Otros equipos:** T02 (pertenencia docente), T11 (notificaciones)
- **Datos / migraciones:** V20 (políticas RLS sobre el read model)
- **Riesgos:** T02 no responde → flag fail-closed y demo con ADMIN

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| #310 | [G06] - [BACKEND] - Implementar endpoint del panel docente con validación de matrícula vía T02 | Regina Cerasulo | Backend, Seguridad | CP3 | La consulta a T02 pasa al resolvedor de alcance (tarea #311) |
| #311 | [G06] - [BACKEND] - Aplicar Row Level Security por course_id y regla anti-comparación | Máximo Cerquatti | Backend, Base de Datos, Seguridad | CP2 | Incluye el resolvedor de alcance, el adaptador de T02 y FORCE RLS; migración V20 |
| #312 | [G06] - [BACKEND] - Emitir STUDENT_AT_HIGH_RISK por outbox solo al pasar a riesgo alto | Damián Baigorria | Backend, Integración | CP3 | Antes: "notificación interna ante transición a riesgo alto" |
| #313 | [G06] - [FRONTEND] - Diseñar vista de panel docente con semáforo de riesgo | Luciano Paz | Frontend, Diseño / UX-UI | CP4 | Pasa de Damián a Luciano |
| #314 | [G06] - [TEST] - Desarrollar tests de RLS: docente A solo ve cohorte A | Luciano Paz | Testing, Seguridad, Base de Datos | CP4 | — |
| #315 | [G06] - [TEST] - Validar regla anti-comparación y emisión de alerta | Luciano Paz | Testing, Seguridad | CP4 | — |
| Nueva | [G06] - [TEST] - Desarrollar specs del panel docente | Bruno Gianoli | Testing, Frontend | CP4 | — |
| #316 | [G06] - [DOCUMENTACION] - Documentar en la wiki los endpoints del panel, la política RLS y el contrato de alertas | Ana Ducart | Documentación, Seguridad | CP4 | Antes: "Especificar endpoints" en el repo; pasa a la wiki |
| #317 | [G06] - [REVISION] - Peer review de seguridad RLS y control en Taiga | Mateo Carballo | Testing, Seguridad | CP3 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar endpoint del panel docente con validación de matrícula vía T02
[G06] - [BACKEND] - Aplicar Row Level Security por course_id y regla anti-comparación
[G06] - [BACKEND] - Emitir STUDENT_AT_HIGH_RISK por outbox solo al pasar a riesgo alto
[G06] - [FRONTEND] - Diseñar vista de panel docente con semáforo de riesgo
[G06] - [TEST] - Desarrollar tests de RLS: docente A solo ve cohorte A
[G06] - [TEST] - Validar regla anti-comparación y emisión de alerta
[G06] - [TEST] - Desarrollar specs del panel docente
[G06] - [DOCUMENTACION] - Documentar en la wiki los endpoints del panel, la política RLS y el contrato de alertas
[G06] - [REVISION] - Peer review de seguridad RLS y control en Taiga
```
