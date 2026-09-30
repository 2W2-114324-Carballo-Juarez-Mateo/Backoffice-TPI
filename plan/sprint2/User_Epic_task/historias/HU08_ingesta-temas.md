# HU08 — Ingesta de datos de los temas con deduplicación

> Épica: [Contratos de Lectura e Ingesta](../epicas/E06_contratos-lectura-ingesta.md) · Taiga: #1628 · Acción: **Reabrir y mover al Sprint 2 (condicionada a contratos)** · Estado actual: Existente (Sprint 1, cerrada)

## Descripción
**Como** Sistema (reporting-service)
**Quiero** consumir los eventos de T07 y T05 con deduplicación y dead letter, detrás de flags
**Para** sumar esas fuentes a los reportes apenas su contrato esté firmado, sin afectar a las fuentes que ya funcionan

## Notas / Observaciones

- **Reglas de negocio:**
  - Los consumidores de T03 y T02 ya funcionan; los de T05 y T07 se agregan con el mismo patrón.
  - Cada fuente tiene un flag apagado hasta que su contrato esté firmado.
  - Se descartan eventos duplicados o de versión menor; los malformados van a dead letter.
- **Validaciones:**
  - El envelope debe traer `eventId` y `eventType`.
  - Un `eventType` desconocido se saltea con log.
- **Datos obligatorios:** eventId, eventType, versión, timestamp, productor
- **Performance:** un evento malformado no bloquea la partición.
- **Seguridad:** los eventos no se exponen por API pública.
- **Accesibilidad:** no aplica.
- **Otros:** condicionada a los contratos con T07 y T05 (C1 y C5).

## Criterios de Aceptación
- **CA1:** Un evento nuevo de T07 o T05 se registra una sola vez.
- **CA2:** Un evento duplicado se descarta.
- **CA3:** Un evento malformado va a dead letter sin frenar la partición.
- **CA4:** Con el flag apagado no se consume nada.

## BDD (mínimo 3 escenarios)

**Característica:** Ingesta con deduplicación

**Escenario 1 — Evento nuevo**
- **Dado** que el flag de T07 está encendido
- **Cuando** llega un evento válido
- **Entonces** se registra y se cuenta

**Escenario 2 — Evento duplicado**
- **Dado** que un evento ya fue procesado
- **Cuando** llega de nuevo
- **Entonces** se descarta y no se duplica

**Escenario 3 — Evento malformado**
- **Dado** que llega un mensaje ilegible
- **Cuando** el consumidor lo recibe
- **Entonces** va a dead letter y la partición sigue

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/reports/ingestion/events → eventos aceptados (ADMIN)
- GET /api/backoffice/reports/health/freshness → estado por fuente (ADMIN)

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Should

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service, Kafka
- **Módulos afectados:** consumidores de T07 y T05, deduplicación, dead letter
- **Otros equipos:** T07 y T05 (contratos)
- **Datos / migraciones:** ninguno nuevo
- **Riesgos:** T05 y T07 sin contrato → flags apagados y tareas en "Necesita información"

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Implementar consumidores de llm.events (T07) y de T05 con flag, deduplicación y DLT | Valentina Maldonado | Backend, Integración, Configuración | CP3 | — |
| Nueva | [G06] - [TEST] - Desarrollar tests de integración de consumidores (nuevo, duplicado, malformado a DLT y flag apagado) | Mateo Carballo | Testing, Integración | CP4 | — |
| Nueva | [G06] - [DOCUMENTACION] - Actualizar el mapeo de contratos de lectura con T07 y T05 | Valentina Maldonado | Documentación, Integración | CP3 | — |
| Nueva | [G06] - [REVISION] - Peer review de los consumidores de T07 y T05 | Luciano Paz | Testing | CP4 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar consumidores de llm.events (T07) y de T05 con flag, deduplicación y DLT
[G06] - [TEST] - Desarrollar tests de integración de consumidores (nuevo, duplicado, malformado a DLT y flag apagado)
[G06] - [DOCUMENTACION] - Actualizar el mapeo de contratos de lectura con T07 y T05
[G06] - [REVISION] - Peer review de los consumidores de T07 y T05
```
