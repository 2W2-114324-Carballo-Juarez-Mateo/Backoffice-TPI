# HU02 — Propagación del cambio de parámetro (Outbox + caché con TTL)

> Épica: [Parámetros Globales](../epicas/E02_parametros-globales.md) · Taiga: #21 · Acción: **Reabrir y mover al Sprint 2** · Estado actual: Existente (Sprint 1, cerrada)

## Descripción
**Como** Sistema (Backoffice)
**Quiero** publicar cada cambio de parámetro en el orden correcto y de forma confiable
**Para** que los temas que aplican el parámetro reciban las versiones en orden y nunca apliquen una versión vieja

## Notas / Observaciones

- **Reglas de negocio:**
  - El cambio y su evento se guardan en la misma transacción (outbox).
  - La clave del parámetro es la clave de partición: las versiones de un mismo parámetro salen en orden.
  - Si una fila anterior de la misma clave está pendiente o en reintento, las más nuevas esperan.
  - Una fila en dead letter no bloquea la clave para siempre.
- **Validaciones:**
  - Una fila `PENDING` más vieja de la misma clave impide publicar una más nueva.
  - Las filas de claves distintas siguen saliendo en paralelo.
- **Datos obligatorios:** clave de partición, versión, estado de la fila, instante de creación
- **Performance:** el publicador procesa lotes cada 5 segundos sin bloquear las peticiones.
- **Seguridad:** el payload de auditoría no incluye secretos ni datos personales.
- **Accesibilidad:** no aplica (proceso interno).
- **Otros:** el orden por clave es el criterio CA4 de la historia original; la PR #46 lo detectó.

## Criterios de Aceptación
- **CA1:** Con v1 en reintento, v2 del mismo parámetro no se publica antes.
- **CA2:** Filas de otros parámetros se publican sin esperar.
- **CA3:** Una fila en dead letter no frena las siguientes de su clave.
- **CA4:** El contrato del consumidor documenta el orden por clave.

## BDD (mínimo 3 escenarios)

**Característica:** Orden estricto del outbox por clave

**Escenario 1 — Reintento de la versión 1**
- **Dado** que v1 del parámetro PAR-05 falló y espera su reintento
- **Cuando** llega v2 del mismo parámetro
- **Entonces** v2 no sale hasta que v1 se publique

**Escenario 2 — Otra clave**
- **Dado** que v1 de PAR-05 está en reintento
- **Cuando** llega un cambio de PAR-08
- **Entonces** el cambio de PAR-08 se publica sin esperar

**Escenario 3 — Fila en dead letter**
- **Dado** que v1 de PAR-05 terminó en dead letter
- **Cuando** llega v2
- **Entonces** v2 se publica y la clave no queda bloqueada

## Prototipo (Mock API / Swagger)
- Sin endpoint nuevo (proceso interno del outbox).

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** backoffice-service, Kafka
- **Módulos afectados:** repositorio del outbox, publicador
- **Otros equipos:** T11 (topics)
- **Datos / migraciones:** sin cambios estructurales; posible índice por estado, clave y fecha
- **Riesgos:** una consulta más lenta del outbox → medir con el índice

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Garantizar el orden estricto del outbox por clave de partición | Luciano Paz | Backend, Base de Datos | CP1 | — |
| Nueva | [G06] - [TEST] - Desarrollar test de integración del orden del outbox con Testcontainers y Kafka | Regina Cerasulo | Testing, Integración | CP4 | — |
| Nueva | [G06] - [DOCUMENTACION] - Documentar el orden por clave en el contrato del consumidor | Luciano Paz | Documentación | CP1 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Garantizar el orden estricto del outbox por clave de partición
[G06] - [TEST] - Desarrollar test de integración del orden del outbox con Testcontainers y Kafka
[G06] - [DOCUMENTACION] - Documentar el orden por clave en el contrato del consumidor
```
