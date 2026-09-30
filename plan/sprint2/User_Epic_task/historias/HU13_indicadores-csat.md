# HU13 — Indicadores consolidados con bloqueo de anonimato

> Épica: [Observabilidad, Reportes y Panel de Riesgo](../epicas/E05_reportes-panel-riesgo.md) · Taiga: #111 · Acción: **Mover al Sprint 2 y actualizar la descripción** · Estado actual: Backlog

## Descripción
**Como** ADMIN o PROFESOR
**Quiero** ver los indicadores de satisfacción (CSAT de 5 estrellas) agregados, y que se oculten las muestras pequeñas
**Para** supervisar la calidad sin desanonimizar respuestas de alumnos ni comparar docentes

## Notas / Observaciones

- **Reglas de negocio:**
  - La fuente de las encuestas es T02 y llega como agregados (conteo por estrella, abstenciones, dimensión y curso cerrado); no se guardan respuestas individuales.
  - KPI de satisfacción = porcentaje de respuestas 4 y 5; detractores = porcentaje de 1 y 2; las abstenciones se informan aparte y no entran al denominador (RF-ENC-10).
  - El PROFESOR solo ve puntajes si el curso tiene al menos 5 respuestas (PAR-18) y el curso ya cerró (RF-ENC-13); si no, solo ve la cantidad de respuestas.
  - El ADMIN ve el consolidado de plataforma y el desglose por curso, sin ranking.
  - No se permiten comparaciones de desempeño entre docentes.
- **Validaciones:**
  - El umbral mínimo se lee de PAR-18 (valor entero positivo).
  - Sin datos de T02 el indicador responde "sin datos".
- **Datos obligatorios:** indicador, valor, meta, curso, cantidad de respuestas
- **Performance:** el tablero carga en menos de 2 segundos.
- **Seguridad:** solo ADMIN accede a `/platform`; otros roles reciben 403; el profesor solo accede a sus cursos.
- **Accesibilidad:** tablero con texto alternativo y navegación por teclado (WCAG AA).
- **Otros:** el resumen de encuestas no guarda autor ni marca de tiempo precisa (RF-ENC-04).

## Criterios de Aceptación
- **CA1:** El ADMIN ve el tablero con los indicadores consolidados y sus metas.
- **CA2:** Un curso con pocas respuestas muestra "muestra insuficiente" y el valor queda oculto.
- **CA3:** El PROFESOR no ve puntajes antes del cierre del curso.
- **CA4:** El tablero no incluye comparaciones entre docentes.

## BDD (mínimo 3 escenarios)

**Característica:** Indicadores consolidados con anonimato

**Escenario 1 — Tablero consolidado**
- **Dado** que hay datos de varios cursos
- **Cuando** el ADMIN abre el tablero
- **Entonces** ve los indicadores con sus metas

**Escenario 2 — Muestra insuficiente**
- **Dado** un curso con 3 respuestas y PAR-18 igual a 5
- **Cuando** el ADMIN consulta el desglose por curso
- **Entonces** se muestra "muestra insuficiente" y el valor queda oculto

**Escenario 3 — Curso sin cerrar**
- **Dado** un curso con 20 respuestas que todavía no cerró
- **Cuando** el PROFESOR consulta sus KPIs
- **Entonces** ve la cantidad de respuestas pero ningún puntaje

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/reports/platform → tablero consolidado (ADMIN)
- GET /api/backoffice/reports/courses/{courseId}/kpis → KPIs de un curso

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Should

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service, T02
- **Módulos afectados:** agregados de indicadores, bloqueo de anonimato, dashboard
- **Otros equipos:** T02 (encuestas agregadas)
- **Datos / migraciones:** V22 (resumen de encuestas, con RLS)
- **Riesgos:** T02 no entrega encuestas agregadas → el KPI responde "sin datos" y se corta el dashboard

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| #319 | [G06] - [BACKEND] - Implementar servicio de agregación analítica de indicadores de plataforma | Joaquín Cortez | Backend, Base de Datos | CP4 | Incluir distribución 1 a 5 y abstenciones; migración V22 |
| #320 | [G06] - [BACKEND] - Implementar mecanismo de protección de anonimato por umbral mínimo | Joaquín Cortez | Backend, Seguridad | CP4 | El umbral es PAR-18, no un parámetro nuevo |
| #321 | [G06] - [BACKEND] - Implementar endpoint exclusivo ADMIN con regla anti-comparación | Joaquín Cortez | Backend, Seguridad | CP4 | Ruta `/api/backoffice/reports/platform` |
| Nueva | [G06] - [BACKEND] - Aplicar el corte al cierre del curso y el reporte de abstenciones en los KPIs | Joaquín Cortez | Backend, Seguridad | CP4 | — |
| #322 | [G06] - [FRONTEND] - Diseñar dashboard de KPIs con tarjetas CSAT y advertencia de anonimato | Mateo Carballo | Frontend, Diseño / UX-UI | CP4 | Pasa de Valentina a Mateo |
| #323 | [G06] - [TEST] - Desarrollar tests de anonimato estadístico y autorización ADMIN | Regina Cerasulo | Testing, Seguridad | CP4 | Pasa de Joaquín a Regina |
| #324 | [G06] - [DOCUMENTACION] - Documentar políticas de privacidad y fórmulas de agregación | Joaquín Cortez | Documentación, Seguridad | CP4 | — |
| #325 | [G06] - [REVISION] - Peer review de privacidad y control en Taiga | Damián Baigorria | Testing, Seguridad | CP4 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar servicio de agregación analítica de indicadores de plataforma
[G06] - [BACKEND] - Implementar mecanismo de protección de anonimato por umbral mínimo
[G06] - [BACKEND] - Implementar endpoint exclusivo ADMIN con regla anti-comparación
[G06] - [BACKEND] - Aplicar el corte al cierre del curso y el reporte de abstenciones en los KPIs
[G06] - [FRONTEND] - Diseñar dashboard de KPIs con tarjetas CSAT y advertencia de anonimato
[G06] - [TEST] - Desarrollar tests de anonimato estadístico y autorización ADMIN
[G06] - [DOCUMENTACION] - Documentar políticas de privacidad y fórmulas de agregación
[G06] - [REVISION] - Peer review de privacidad y control en Taiga
```
