# HU14 — Umbrales de aviso y acceso al tablero

> Épica: [Observabilidad, Reportes y Panel de Riesgo](../epicas/E05_reportes-panel-riesgo.md) · Taiga: #34 · Acción: **Mover al Sprint 2 como extra y actualizar la descripción** · Estado actual: Backlog

## Descripción
**Como** ADMIN
**Quiero** configurar umbrales para los indicadores y ver las alertas activas cuando uno baja de lo esperado, con acceso solo para administradores
**Para** reaccionar a tiempo ante un indicador fuera de rango

## Notas / Observaciones

- **Reglas de negocio:**
  - Los umbrales de aviso los configura el ADMIN (por ejemplo, satisfacción mínima).
  - Si un indicador cae por debajo del umbral, se crea una alerta interna activa y se resuelve cuando vuelve al rango.
  - No se emite `THRESHOLD_BREACHED`: T11 no lo registró; las alertas son internas.
  - Solo el rol ADMIN accede al tablero y a las alertas.
- **Validaciones:**
  - El umbral debe ser válido (`min ≤ max`).
  - Una alerta activa no se duplica para el mismo umbral.
- **Datos obligatorios:** indicador, umbral mínimo y máximo, valor actual, estado
- **Performance:** la evaluación corre en segundo plano sin afectar a los usuarios.
- **Seguridad:** solo ADMIN; otros roles reciben 403.
- **Accesibilidad:** las alertas se muestran con texto claro y por teclado.
- **Otros:** es una historia extra: solo arranca cuando el núcleo del dueño está mergeado.

## Criterios de Aceptación
- **CA1:** El ADMIN puede configurar un umbral para un indicador.
- **CA2:** Si el indicador baja del umbral, aparece una alerta activa.
- **CA3:** Un rol que no es ADMIN recibe 403 al acceder.

## BDD (mínimo 3 escenarios)

**Característica:** Umbrales y alertas internas

**Escenario 1 — Configuración de umbral**
- **Dado** que el ADMIN está en el panel de umbrales
- **Cuando** fija la satisfacción mínima en 80 %
- **Entonces** el umbral queda guardado y aplica al indicador

**Escenario 2 — Indicador bajo el umbral**
- **Dado** que el umbral es 80 %
- **Cuando** el indicador baja a 74 %
- **Entonces** aparece una alerta activa

**Escenario 3 — Acceso denegado**
- **Dado** un usuario que no es ADMIN
- **Cuando** intenta abrir el panel de umbrales
- **Entonces** responde 403 y no muestra los indicadores

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/reports/alerts → alertas activas
- CRUD /api/backoffice/reports/alert-thresholds → umbrales (ADMIN)

## Estimación / Prioridad
- **Puntos (Fibonacci):** 3
- **Prioridad (MoSCoW):** Could

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service
- **Módulos afectados:** umbrales, evaluador, alertas, permisos
- **Otros equipos:** T11 (si registra el evento de umbral)
- **Datos / migraciones:** V23 (umbrales y alertas)
- **Riesgos:** depende de los indicadores de HU13

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| #326 | [G06] - [BACKEND] - Implementar modelo de umbrales y endpoints CRUD exclusivo ADMIN | Regina Cerasulo | Backend, Base de Datos | CP5 | Migración V23 |
| #327 | [G06] - [BACKEND] - Implementar evaluador periódico de métricas contra umbrales y despacho de alertas | Regina Cerasulo | Backend | CP5 | Alertas internas; no emite THRESHOLD_BREACHED. Esta es la tarea del evaluador (no #330) |
| #328 | [G06] - [FRONTEND] - Diseñar panel de configuración de umbrales y lista de alertas activas | Luciano Paz | Frontend, Diseño / UX-UI | CP5 | — |
| #329 | [G06] - [TEST] - Desarrollar tests de evaluación de umbrales y autorización | Máximo Cerquatti | Testing | CP5 | — |
| #330 | [G06] - [DOCUMENTACION] - Documentar en la wiki el catálogo de umbrales y alertas | Ana Ducart | Documentación | CP5 | Pasa a la wiki |
| #331 | [G06] - [REVISION] - Peer review final y cierre en Taiga | Joaquín Cortez | Testing | CP5 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar modelo de umbrales y endpoints CRUD exclusivo ADMIN
[G06] - [BACKEND] - Implementar evaluador periódico de métricas contra umbrales y despacho de alertas
[G06] - [FRONTEND] - Diseñar panel de configuración de umbrales y lista de alertas activas
[G06] - [TEST] - Desarrollar tests de evaluación de umbrales y autorización
[G06] - [DOCUMENTACION] - Documentar en la wiki el catálogo de umbrales y alertas
[G06] - [REVISION] - Peer review final y cierre en Taiga
```
