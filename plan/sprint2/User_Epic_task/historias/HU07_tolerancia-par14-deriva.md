# HU07 — Aprobación por tolerancia (PAR-14) y fallback por deriva

> Épica: [Configuración de Modelos LLM y Golden Set](../epicas/E03_modelos-llm-golden-set.md) · Taiga: #29 · Acción: **Mover al Sprint 2 (condicionada a T07) y actualizar la descripción** · Estado actual: Existente (Sprint 1)

## Descripción
**Como** ADMIN
**Quiero** gobernar la tolerancia de calibración (PAR-14) y ver el veredicto y la deriva del modelo activo
**Para** asegurar la equidad de las calificaciones y enterarme si el modelo se degrada

## Notas / Observaciones

- **Reglas de negocio:**
  - T07 calcula el error, decide el veredicto y emite la deriva; el Backoffice gobierna el valor de PAR-14 y lo muestra.
  - PAR-14 tiene la forma `{"average", "dimension"}` y T07 lo lee con `backoffice.parameters.read`.
  - Si T07 conmuta automáticamente a un respaldo por deriva, el Backoffice muestra un banner.
- **Validaciones:**
  - PAR-14: ambos valores numéricos y `0 < average ≤ dimension ≤ 100`.
  - El estado de calibración se lee de T07; si no hay dato se informa "sin datos".
- **Datos obligatorios:** veredicto, marca de deriva, tolerancia
- **Performance:** el estado se consulta sin bloquear la pantalla.
- **Seguridad:** solo ADMIN edita PAR-14 y ve el detalle.
- **Accesibilidad:** el veredicto y la deriva se indican con color y texto.
- **Otros:** condicionada a C1 (ruta de T07).

## Criterios de Aceptación
- **CA1:** El ADMIN ve el veredicto y la marca de deriva del modelo activo.
- **CA2:** Si T07 conmutó por deriva, se muestra el banner de conmutación automática.
- **CA3:** PAR-14 solo se guarda con una forma y un rango válidos.

## BDD (mínimo 3 escenarios)

**Característica:** Veredicto y deriva del modelo activo

**Escenario 1 — Modelo aprobado**
- **Dado** que T07 aprobó al modelo activo
- **Cuando** el ADMIN abre el estado de calibración
- **Entonces** ve el veredicto aprobado

**Escenario 2 — Deriva detectada**
- **Dado** que T07 detectó deriva y pasó a un respaldo
- **Cuando** el ADMIN abre el Backoffice
- **Entonces** ve el banner de conmutación automática

**Escenario 3 — Tolerancia inválida**
- **Dado** que el ADMIN ingresa `average` mayor que `dimension`
- **Cuando** guarda PAR-14
- **Entonces** responde 400 y no guarda

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/llm/calibration/status → estado de calibración del modelo activo

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Should

## Dependencias / Impactos
- **Servicios / APIs:** backoffice-service, T07
- **Módulos afectados:** estado de calibración, indicador y banner
- **Otros equipos:** T07 (veredicto y deriva)
- **Datos / migraciones:** ninguno
- **Riesgos:** T07 no confirma → stub con flag; la parte de PAR-14 no tiene esa dependencia

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Implementar endpoint del estado de calibración del modelo activo | Bruno Gianoli | Backend, Integración | CP4 | — |
| #284 | [G06] - [FRONTEND] - Diseñar indicador visual de deriva y banner de conmutación automática | Valentina Maldonado | Frontend, Diseño / UX-UI | CP4 | Pasa de Joaquín a Valentina |
| #286 | [G06] - [DOCUMENTACION] - Documentar algoritmo de evaluación, umbrales PAR-14 y reglas de drift | Bruno Gianoli | Documentación, Análisis | CP4 | — |
| Nueva | [G06] - [TEST] - Desarrollar tests del estado de calibración del modelo activo | Máximo Cerquatti | Testing, Integración | CP4 | — |
| Nueva | [G06] - [REVISION] - Peer review de la fachada de veredicto y deriva | Damián Baigorria | Testing | CP4 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar endpoint del estado de calibración del modelo activo
[G06] - [FRONTEND] - Diseñar indicador visual de deriva y banner de conmutación automática
[G06] - [DOCUMENTACION] - Documentar algoritmo de evaluación, umbrales PAR-14 y reglas de drift
[G06] - [TEST] - Desarrollar tests del estado de calibración del modelo activo
[G06] - [REVISION] - Peer review de la fachada de veredicto y deriva
```
