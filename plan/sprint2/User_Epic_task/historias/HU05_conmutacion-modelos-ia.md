# HU05 — Sustitución y conmutación de modelos de IA

> Épica: [Configuración de Modelos LLM y Golden Set](../epicas/E03_modelos-llm-golden-set.md) · Taiga: #26 · Acción: **Mover al Sprint 2 y actualizar la descripción** · Estado actual: Existente (Sprint 1)

## Descripción
**Como** ADMIN
**Quiero** activar un modelo aprobado como el que se usa y conmutar entre modelos homologados, desde el Backoffice
**Para** cambiar de proveedor según costo o calidad, sin interrumpir las evaluaciones en curso y avisando a los servicios que lo consumen

## Notas / Observaciones

- **Reglas de negocio:**
  - El Backoffice es fachada de T07: T07 decide si el modelo está aprobado y garantiza un solo activo por función.
  - Solo se puede activar un modelo aprobado; si no, se rechaza.
  - El aviso `MODEL_CHANGED` lo publica T07; el Backoffice deja de emitir `ModelProviderChanged`.
- **Validaciones:**
  - El modelo debe estar aprobado.
  - No se puede activar un modelo inexistente o retirado.
- **Datos obligatorios:** modelo a activar
- **Performance:** la conmutación no interrumpe evaluaciones en curso.
- **Seguridad:** solo ADMIN opera.
- **Accesibilidad:** el modal de conmutación muestra el modelo actual y el nuevo, pide confirmación explícita, atrapa el foco y se cierra con Esc.
- **Otros:** la pantalla 10 deja de usar datos en memoria y usa `/api/backoffice/llm/models`.

## Criterios de Aceptación
- **CA1:** Al activar un modelo aprobado, queda activo y el anterior pasa a reserva (lo resuelve T07).
- **CA2:** Si T07 responde 409 por modelo no aprobado, el ADMIN ve un mensaje claro y no cambia nada.
- **CA3:** Si T07 no está disponible, la respuesta es 503.
- **CA4:** El modal exige confirmación explícita.

## BDD (mínimo 3 escenarios)

**Característica:** Conmutación de modelos de IA

**Escenario 1 — Conmutación exitosa**
- **Dado** que dos modelos están aprobados
- **Cuando** el ADMIN activa el segundo y confirma
- **Entonces** queda activo y el anterior pasa a reserva

**Escenario 2 — Modelo no aprobado**
- **Dado** un modelo que no pasó la revisión
- **Cuando** el ADMIN intenta activarlo
- **Entonces** responde 409 y no cambia nada

**Escenario 3 — T07 caído**
- **Dado** que T07 no responde
- **Cuando** el ADMIN activa un modelo
- **Entonces** responde 503 con un mensaje claro

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/llm/models
- GET /api/backoffice/llm/models/active
- POST /api/backoffice/llm/models/{id}/activate

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** backoffice-service, T07
- **Módulos afectados:** cliente real de modelos, pantalla 10, modal
- **Otros equipos:** T07 (publica `MODEL_CHANGED`)
- **Datos / migraciones:** ninguno
- **Riesgos:** T07 no confirma la ruta → stub con flag

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Implementar cliente real de modelos evaluadores (listar, activo, desplegar, activar y borrar) | Mateo Carballo | Backend, Integración | CP3 | — |
| #276 | [G06] - [FRONTEND] - Implementar modal de conmutación con advertencia de impacto | Mateo Carballo | Frontend, Diseño / UX-UI | CP3 | — |
| Nueva | [G06] - [FRONTEND] - Conectar la pantalla de modelos al backend y quitar los datos en memoria | Mateo Carballo | Frontend, Integración | CP3 | — |
| Nueva | [G06] - [TEST] - Desarrollar tests con WireMock de la activación de modelos (200, 409 y 503) | Luciano Paz | Testing, Integración | CP4 | — |
| Nueva | [G06] - [DOCUMENTACION] - Registrar en el contrato que MODEL_CHANGED lo publica T07 | Máximo Cerquatti | Documentación, Integración | CP2 | Va dentro de la tarea C1 (mismo archivo de contrato) |
| Nueva | [G06] - [REVISION] - Peer review de la fachada de modelos y del mapeo de errores | Joaquín Cortez | Testing | CP3 | — |
| Nueva | [G06] - [TEST] - Desarrollar specs de las pantallas de proveedores y modelos conectadas | Valentina Maldonado | Testing, Frontend | CP4 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar cliente real de modelos evaluadores (listar, activo, desplegar, activar y borrar)
[G06] - [FRONTEND] - Implementar modal de conmutación con advertencia de impacto
[G06] - [FRONTEND] - Conectar la pantalla de modelos al backend y quitar los datos en memoria
[G06] - [TEST] - Desarrollar tests con WireMock de la activación de modelos (200, 409 y 503)
[G06] - [DOCUMENTACION] - Registrar en el contrato que MODEL_CHANGED lo publica T07
[G06] - [REVISION] - Peer review de la fachada de modelos y del mapeo de errores
[G06] - [TEST] - Desarrollar specs de las pantallas de proveedores y modelos conectadas
```
