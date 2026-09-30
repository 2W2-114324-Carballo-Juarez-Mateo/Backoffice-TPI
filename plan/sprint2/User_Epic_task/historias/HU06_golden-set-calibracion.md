# HU06 — Gestión del golden set y ejecución de revisión

> Épica: [Configuración de Modelos LLM y Golden Set](../epicas/E03_modelos-llm-golden-set.md) · Taiga: #27 · Acción: **Mover al Sprint 2 (condicionada a T07) y actualizar la descripción** · Estado actual: Existente (Sprint 1)

## Descripción
**Como** ADMIN
**Quiero** ver y gestionar desde el Backoffice el perfil de calibración y las corridas de calibración de los modelos
**Para** saber con datos si un modelo es confiable antes de usarlo

## Notas / Observaciones

- **Reglas de negocio:**
  - El golden set y el cálculo del error los hace T07; el Backoffice es solo fachada (perfil y corridas).
  - Una corrida devuelve `maeFinal`, `maxIndividualError` y el veredicto que decide T07.
  - Esta historia está condicionada a que T07 confirme `/api/llm/admin/*` el 02/10; si no, queda con stub y flag.
- **Validaciones:**
  - El modelo debe existir en T07.
  - Debe haber casos de referencia cargados en T07.
- **Datos obligatorios:** modelo, corrida, resultado
- **Performance:** la corrida se procesa en T07 en segundo plano.
- **Seguridad:** solo ADMIN ve el detalle.
- **Accesibilidad:** los resultados se muestran con formato claro y navegables por teclado.
- **Otros:** no se implementa cálculo de error en el Backoffice (la rama del golden set local se archiva).

## Criterios de Aceptación
- **CA1:** El ADMIN consulta el perfil de calibración institucional desde el Backoffice.
- **CA2:** El ADMIN crea, lista y ve el detalle de una corrida con el veredicto de T07.
- **CA3:** Si T07 no confirma el contrato, la historia queda con stub y flag sin romper el resto.

## BDD (mínimo 3 escenarios)

**Característica:** Calibración de modelos como fachada de T07

**Escenario 1 — Corrida exitosa**
- **Dado** que hay casos de referencia y un modelo candidato
- **Cuando** el ADMIN crea una corrida
- **Entonces** ve el veredicto y los errores que calculó T07

**Escenario 2 — Sin casos de referencia**
- **Dado** que T07 no tiene casos cargados
- **Cuando** el ADMIN crea una corrida
- **Entonces** se informa que faltan los casos

**Escenario 3 — Consulta del detalle**
- **Dado** que hay corridas guardadas
- **Cuando** el ADMIN abre una
- **Entonces** ve modelo, errores, veredicto y fecha

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/llm/golden-set/profile
- POST /api/backoffice/llm/golden-set/runs
- GET /api/backoffice/llm/golden-set/runs

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Should

## Dependencias / Impactos
- **Servicios / APIs:** backoffice-service, T07
- **Módulos afectados:** fachada de perfil y corridas, pantallas de calibración
- **Otros equipos:** T07 (golden set, cálculo y veredicto)
- **Datos / migraciones:** ninguno
- **Riesgos:** T07 no responde antes del 02/10 → stub con flag y corte de la historia

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| #3537 | [G06] - [BACKEND] - Implementar fachada del perfil de calibración institucional sobre T07 | Joaquín Cortez | Backend, Integración | CP3 | Renombrar en Taiga: antes decía "Implementar casos del golden set" |
| #3538 | [G06] - [FRONTEND] - Implementar pantalla del perfil de calibración | Joaquín Cortez | Frontend, Integración | CP3 | Renombrar en Taiga |
| #3539 | [G06] - [BACKEND] - Implementar fachada de corridas de calibración sobre T07 | Bruno Gianoli | Backend, Integración | CP3 | Renombrar en Taiga: antes decía "corridas y cálculo de MAE" |
| #3540 | [G06] - [FRONTEND] - Implementar pantalla de corridas de calibración | Mateo Carballo | Frontend, Integración | CP4 | Pasa de Bruno a Mateo; renombrar en Taiga |
| Nueva | [G06] - [TEST] - Desarrollar tests con WireMock de la fachada de calibración | Máximo Cerquatti | Testing, Integración | CP4 | — |
| Nueva | [G06] - [DOCUMENTACION] - Diagramar la secuencia ADMIN, Backoffice y T07 de la calibración | Ana Ducart | Documentación, Diseño / UX-UI | CP4 | — |
| Nueva | [G06] - [REVISION] - Peer review de la fachada de calibración | Regina Cerasulo | Testing | CP4 | — |
| Nueva | [G06] - [DOCUMENTACION] - Documentar en OpenAPI la fachada de calibración | Joaquín Cortez | Documentación | CP4 | — |
| Nueva | [G06] - [BACKEND] - Archivar la rama del golden set local y borrarla (Opción A) | Bruno Gianoli | Gestión, DevOps | CP0 | Tag `archive/s6-golden-set-local` |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar fachada del perfil de calibración institucional sobre T07
[G06] - [FRONTEND] - Implementar pantalla del perfil de calibración
[G06] - [BACKEND] - Implementar fachada de corridas de calibración sobre T07
[G06] - [FRONTEND] - Implementar pantalla de corridas de calibración
[G06] - [TEST] - Desarrollar tests con WireMock de la fachada de calibración
[G06] - [DOCUMENTACION] - Diagramar la secuencia ADMIN, Backoffice y T07 de la calibración
[G06] - [REVISION] - Peer review de la fachada de calibración
[G06] - [DOCUMENTACION] - Documentar en OpenAPI la fachada de calibración
[G06] - [BACKEND] - Archivar la rama del golden set local y borrarla (Opción A)
```
