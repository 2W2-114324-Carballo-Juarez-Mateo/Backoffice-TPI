# HU09 — Exportación de reportes

> Épica: [Observabilidad, Reportes y Panel de Riesgo](../epicas/E05_reportes-panel-riesgo.md) · Taiga: #20 · Acción: **Sacar del sprint de G01 y mover al Sprint 2 como extra; actualizar la descripción** · Estado actual: Hoy en "G01 - Sprint 1" (sprint de otro grupo)

## Descripción
**Como** ADMIN o PROFESOR
**Quiero** pedir que se genere un archivo CSV con los reportes y que me avise cuando esté listo
**Para** obtener constancias que se puedan usar fuera de la plataforma sin que la pantalla se trabe mientras se genera

## Notas / Observaciones

- **Reglas de negocio:**
  - La solicitud de exportación no bloquea la interfaz: responde al instante y el archivo se genera en segundo plano.
  - Al terminar se publica `EXPORT_READY` en `notifications.events` y se habilita un enlace temporal de descarga.
  - El profesor solo puede exportar sus propias comisiones y el anonimato de las encuestas se conserva.
  - En este sprint el formato es CSV; el PDF queda para el siguiente sprint.
- **Validaciones:**
  - La solicitud debe indicar el tipo de reporte y el formato.
  - Solo se exportan datos a los que el usuario tiene acceso.
  - Las celdas que empiezan con `=`, `+`, `-` o `@` se neutralizan (inyección de fórmulas).
- **Datos obligatorios:** tipo de reporte, formato, alcance (comisión o consolidado)
- **Performance:** la generación es en segundo plano; el archivo queda disponible dentro de un tiempo razonable.
- **Seguridad:** el trabajo guarda el alcance de quien lo pidió y lo vuelve a aplicar al ejecutarse; solo quien lo pidió descarga; los enlaces vencen.
- **Accesibilidad:** el CSV se abre con texto claro.
- **Otros:** historia extra: solo arranca cuando el núcleo del dueño está mergeado.

## Criterios de Aceptación
- **CA1:** Al solicitar una exportación, el sistema responde al instante con un identificador.
- **CA2:** Al terminar de generarse, se avisa con `EXPORT_READY` y queda disponible el enlace temporal.
- **CA3:** El profesor solo exporta sus comisiones.
- **CA4:** Si el enlace ya venció, la descarga responde 410 y no se puede acceder.

## BDD (mínimo 3 escenarios)

**Característica:** Exportación de reportes

**Escenario 1 — Solicitud no bloqueante**
- **Dado** que se pide exportar un reporte en CSV
- **Cuando** se envía la solicitud
- **Entonces** responde al instante con un identificador y, al terminar, avisa con el enlace de descarga

**Escenario 2 — Alcance del docente**
- **Dado** que un PROFESOR exporta calificaciones
- **Cuando** se genera el archivo
- **Entonces** el archivo contiene solo sus comisiones asignadas

**Escenario 3 — Enlace vencido**
- **Dado** que el enlace temporal de descarga ya venció
- **Cuando** se intenta descargar
- **Entonces** responde 410 y no permite el acceso

## Prototipo (Mock API / Swagger)
- POST /api/backoffice/reports/exports → solicitar exportación
- GET /api/backoffice/reports/exports/{exportId}/download → descargar

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Could

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service
- **Módulos afectados:** generación de archivos, enlaces temporales, notificaciones
- **Otros equipos:** T11 (notificaciones)
- **Datos / migraciones:** V25 (trabajo de exportación)
- **Riesgos:** archivos grandes → generación en segundo plano

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| #295 | [G06] - [BACKEND] - Implementar endpoint asíncrono de solicitud de exportación | Damián Baigorria | Backend, Integración | CP5 | Sacar del "G01 - Sprint 1"; migración V25 |
| #296 | [G06] - [BACKEND] - Implementar generador de archivos en streaming para CSV | Damián Baigorria | Backend | CP5 | Acotar a CSV |
| #297 | [G06] - [BACKEND] - Implementar enlace temporal con vencimiento y validación de alcance por rol | Damián Baigorria | Backend, Seguridad | CP5 | — |
| #299 | [G06] - [FRONTEND] - Diseñar diálogo de exportación y notificación de descarga | Valentina Maldonado | Frontend, Diseño / UX-UI | CP5 | — |
| #300 | [G06] - [TEST] - Desarrollar tests de generación, seguridad de alcance y vencimiento | Máximo Cerquatti | Testing, Seguridad | CP5 | — |
| #301 | [G06] - [DOCUMENTACION] - Documentar en la wiki el flujo asíncrono de exportación y el aviso EXPORT_READY | Ana Ducart | Documentación | CP5 | Pasa a la wiki |
| #302 | [G06] - [REVISION] - Peer review de manejo de streams y memoria y control en Taiga | Luciano Paz | Testing | CP5 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar endpoint asíncrono de solicitud de exportación
[G06] - [BACKEND] - Implementar generador de archivos en streaming para CSV
[G06] - [BACKEND] - Implementar enlace temporal con vencimiento y validación de alcance por rol
[G06] - [FRONTEND] - Diseñar diálogo de exportación y notificación de descarga
[G06] - [TEST] - Desarrollar tests de generación, seguridad de alcance y vencimiento
[G06] - [DOCUMENTACION] - Documentar en la wiki el flujo asíncrono de exportación y el aviso EXPORT_READY
[G06] - [REVISION] - Peer review de manejo de streams y memoria y control en Taiga
```
