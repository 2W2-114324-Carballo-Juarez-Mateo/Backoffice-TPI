# HT01 — Registro y gestión de contratos entre temas

> Épica: [Contratos de Lectura e Ingesta](../epicas/E06_contratos-lectura-ingesta.md) · Taiga: #1629 · Acción: **Reabrir y mover al Sprint 2** · Estado actual: Existente (Sprint 1, cerrada)

## Descripción
**Como** Equipo de desarrollo del Backoffice
**Quiero** tener firmado, tema por tema, qué datos leemos, por qué mecanismo y con qué payload
**Para** programar los reportes contra contratos reales y no contra supuestos, sin depender de que otro equipo se acuerde

## Notas / Observaciones

- **Reglas de negocio:**
  - Cada contrato se firma en el `.md` del tema con fecha, responsables de ambos lados y versión; un acuerdo por chat no cuenta.
  - Se actualizan juntos el `.md` del tema, la tabla de estado y la fila de firma de `CONTRATOS.md`.
  - Sin contrato firmado se programa contra un puerto con flag y una alternativa documentada.
  - Fecha tope de los pedidos: el 02/10 (checkpoint 2).
- **Validaciones:**
  - El envelope es de 6 campos y los topics son los ratificados por T11 (v3).
  - La solicitud a T02 pide encuestas agregadas, no respuestas individuales.
- **Datos obligatorios:** tema, versión, fecha de acuerdo, responsables, evidencia
- **Performance:** no aplica.
- **Seguridad:** las solicitudes no incluyen datos personales ni secretos.
- **Accesibilidad:** no aplica.
- **Otros:** cada dueño de contrato completa su fila en su propia PR; Ana lleva el seguimiento en Taiga.

## Criterios de Aceptación
- **CA1:** Los contratos con T01, T02, T03, T05, T07, T08, T10 y T11 tienen estado y fila de firma completos, o el motivo en la tarjeta de seguimiento.
- **CA2:** La solicitud a T02 usa el envelope de 6 campos y pide pertenencia docente, padrón y encuestas agregadas.
- **CA3:** Existe la primera solicitud formal a T05.

## BDD (mínimo 3 escenarios)

**Característica:** Gestión de contratos entre temas

**Escenario 1 — Contrato firmado**
- **Dado** que T03 confirmó los valores del resultado del desafío
- **Cuando** se registra el acuerdo
- **Entonces** la fila de T03 queda con fecha, responsables y versión

**Escenario 2 — Contrato sin respuesta**
- **Dado** que T02 no respondió el 02/10
- **Cuando** se revisa el checkpoint
- **Entonces** el servicio queda con flag y la tarea pasa a "Necesita información"

**Escenario 3 — Tema sin solicitud**
- **Dado** que T05 no tiene solicitud
- **Cuando** se redacta la primera
- **Entonces** el contrato pasa de "Pendiente" a "Solicitud lista"

## Prototipo (Mock API / Swagger)
- Sin endpoint (documentación): `CONTRATOS.md`, `CONTRATOS_T0X_*.md`

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** ninguno
- **Módulos afectados:** documentación de contratos
- **Otros equipos:** T01, T02, T03, T05, T07, T08, T10, T11
- **Datos / migraciones:** ninguno
- **Riesgos:** T02 y T05 sin respuesta → bloquean panel docente y encuestas; se trabaja con flags

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [DOCUMENTACION] - Confirmar con T07 la llamada a /api/llm/admin/* y registrar las firmas de T07 y T01 | Máximo Cerquatti | Integración, Seguridad, Documentación | CP2 | Incluye corregir el §6 del contrato de T07 como fachada |
| Nueva | [G06] - [DOCUMENTACION] - Confirmar con T11 la materialización de notifications.events y el payload de los avisos | Mateo Carballo | Integración, Gestión | CP2 | — |
| Nueva | [G06] - [DOCUMENTACION] - Rehacer la solicitud de contratos a T02 (pertenencia docente, padrón y encuestas agregadas) | Damián Baigorria | Integración, Gestión | CP0 | — |
| Nueva | [G06] - [DOCUMENTACION] - Redactar la primera solicitud de contratos a T05 (entregas y resultados prácticos) | Damián Baigorria | Integración, Gestión | CP0 | — |
| Nueva | [G06] - [DOCUMENTACION] - Dar seguimiento al contrato con T10 (eventos, vidas agotadas y retención) y registrar su firma | Luciano Paz | Integración, Gestión | CP2 | — |
| Nueva | [G06] - [DOCUMENTACION] - Confirmar con T03 los valores posibles del resultado del desafío y registrar su firma | Valentina Maldonado | Integración, Investigación | CP2 | — |
| Nueva | [G06] - [DOCUMENTACION] - Registrar la firma del contrato con T08 | Bruno Gianoli | Integración, Gestión | CP2 | — |
| Nueva | [G06] - [DOCUMENTACION] - Crear una tarjeta de seguimiento por cada contrato con fecha tope el 02/10 | Ana Ducart | Gestión, Integración | CP0 | — |

### Texto para copiar en Taiga

```text
[G06] - [DOCUMENTACION] - Confirmar con T07 la llamada a /api/llm/admin/* y registrar las firmas de T07 y T01
[G06] - [DOCUMENTACION] - Confirmar con T11 la materialización de notifications.events y el payload de los avisos
[G06] - [DOCUMENTACION] - Rehacer la solicitud de contratos a T02 (pertenencia docente, padrón y encuestas agregadas)
[G06] - [DOCUMENTACION] - Redactar la primera solicitud de contratos a T05 (entregas y resultados prácticos)
[G06] - [DOCUMENTACION] - Dar seguimiento al contrato con T10 (eventos, vidas agotadas y retención) y registrar su firma
[G06] - [DOCUMENTACION] - Confirmar con T03 los valores posibles del resultado del desafío y registrar su firma
[G06] - [DOCUMENTACION] - Registrar la firma del contrato con T08
[G06] - [DOCUMENTACION] - Crear una tarjeta de seguimiento por cada contrato con fecha tope el 02/10
```
