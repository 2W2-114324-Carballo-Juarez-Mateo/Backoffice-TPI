# HU01 — Modificación y versionado de parámetros globales

> Épica: [Parámetros Globales](../epicas/E02_parametros-globales.md) · Taiga: #17 · Acción: **Mover al Sprint 2 (en "In progress")** · Estado actual: Existente (Sprint 1)

## Descripción
**Como** ADMIN
**Quiero** modificar el valor de un parámetro global (PAR-01 a PAR-24) con versionado e historial, y que el registro esté completo y validado
**Para** cambiar las reglas de la plataforma sin tocar código y conservar quién cambió qué y cuándo

## Notas / Observaciones

- **Reglas de negocio:**
  - El registro contiene los parámetros del Backoffice con su estado (confirmado o candidato), tipo y consumidores.
  - Cada cambio crea una versión nueva y una fila inmutable en el historial; los cambios rigen hacia adelante.
  - PAR-12 (vidas iniciales y máximas) es del Backoffice y lo consume T10 (decisión del 29/09).
  - El PROFESOR puede leer los parámetros pero no editarlos.
- **Validaciones:**
  - PAR-14 tiene la forma `{"average": n, "dimension": m}` con `0 < average ≤ dimension ≤ 100` (a confirmar con T07 el renombre desde `promedio`).
  - PAR-12 cumple `1 ≤ initialLives ≤ maxLives`.
  - El bloqueo optimista rechaza una versión desactualizada.
- **Datos obligatorios:** clave del parámetro, valor, versión, motivo, usuario
- **Performance:** la lectura de un parámetro responde en menos de 200 ms con caché.
- **Seguridad:** solo ADMIN modifica; PROFESSOR solo lee; los servicios leen con el permiso `backoffice.parameters.read`.
- **Accesibilidad:** la pantalla de parámetros se usa por teclado y el PROFESOR ve una vista de solo lectura sin botones de edición.
- **Otros:** cada cambio se publica por outbox en `administration.events` con la clave del parámetro como clave de partición.

## Criterios de Aceptación
- **CA1:** El ADMIN modifica un parámetro y se crea una versión nueva con su historial.
- **CA2:** PAR-14 y PAR-12 se rechazan si no cumplen su forma y su rango.
- **CA3:** El PROFESOR ve los parámetros en solo lectura, sin acción de edición.
- **CA4:** Existe un test de integración sobre PostgreSQL real que cubre versión, bloqueo optimista, historial e `Idempotency-Key`.

## BDD (mínimo 3 escenarios)

**Característica:** Modificación y versionado de parámetros

**Escenario 1 — Cambio de un parámetro**
- **Dado** que el ADMIN edita PAR-05 con una versión vigente
- **Cuando** guarda el nuevo valor
- **Entonces** se crea la versión siguiente y queda la fila en el historial

**Escenario 2 — Valor fuera de rango**
- **Dado** que el ADMIN intenta guardar PAR-14 con `average` mayor que `dimension`
- **Cuando** envía el cambio
- **Entonces** responde 400 con un mensaje claro y no guarda nada

**Escenario 3 — Profesor en solo lectura**
- **Dado** que un PROFESOR abre la pantalla de parámetros
- **Cuando** la pantalla carga
- **Entonces** ve los valores sin botones de edición y la ruta de edición le responde 403

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/parameters → listar parámetros
- PUT /api/backoffice/parameters/{key} → modificar un parámetro
- GET /api/backoffice/parameters/{key}/history → historial

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** backoffice-service
- **Módulos afectados:** registro de parámetros, validaciones, outbox
- **Otros equipos:** T07 (forma de PAR-14), T10 (consume PAR-12)
- **Datos / migraciones:** V24 (PAR-12 y alineación con Skill Hub, PR #56)
- **Riesgos:** renombrar `promedio` a `average` cambia un valor ya cargado → confirmarlo con T07 antes de mergear

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Validar PAR-14 con la forma oficial y registrar PAR-12 (PR #56) | Mateo Carballo | Backend, Base de Datos, Configuración | CP0 | PR #56 ya abierta; usa la migración V24 |
| Nueva | [G06] - [REVISION] - Revisar la PR #56 de alineación de parámetros con Skill Hub | Damián Baigorria | Testing | CP0 | — |
| Nueva | [G06] - [TEST] - Desarrollar test de integración del servicio de parámetros con Testcontainers | Mateo Carballo | Testing, Base de Datos | CP1 | Rescatado de la rama `feature/us-01-testcontainers`, solo el test |
| Nueva | [G06] - [TEST] - Desarrollar tests de validación de PAR-14 y PAR-12 del registro | Máximo Cerquatti | Testing | CP2 | — |
| #3331 | [G06] - [FRONTEND] - Restringir edición de parámetros para el rol PROFESOR | Damián Baigorria | Frontend, Seguridad | CP3 | Pasa de Bruno a Damián |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Validar PAR-14 con la forma oficial y registrar PAR-12 (PR #56)
[G06] - [REVISION] - Revisar la PR #56 de alineación de parámetros con Skill Hub
[G06] - [TEST] - Desarrollar test de integración del servicio de parámetros con Testcontainers
[G06] - [TEST] - Desarrollar tests de validación de PAR-14 y PAR-12 del registro
[G06] - [FRONTEND] - Restringir edición de parámetros para el rol PROFESOR
```
