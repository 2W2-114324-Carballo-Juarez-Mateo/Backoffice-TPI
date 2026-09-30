# HU11 — Read model y cálculo de riesgo por cohorte

> Épica: [Observabilidad, Reportes y Panel de Riesgo](../epicas/E05_reportes-panel-riesgo.md) · Taiga: #25 · Acción: **Mover al Sprint 2 y actualizar la descripción** · Estado actual: Backlog

## Descripción
**Como** Sistema (reporting-service)
**Quiero** armar el resumen de cada curso-cohorte y calcular el estado de riesgo de cada alumno
**Para** que el docente pueda ver en el panel quién necesita ayuda (el cálculo queda listo para mostrarse)

## Notas / Observaciones

- **Reglas de negocio:**
  - El resumen se arma por curso-cohorte desde los datos recibidos (ingesta).
  - El estado de riesgo se evalúa en este orden y gana la primera condición que se cumple:
  - ROJO: inactividad mayor a 10 días, o reprobación mayor a 60 % (con 3 intentos o más), o vidas agotadas (con flag).
  - AMARILLO: inactividad de 5 a 10 días, o reprobación de 40 a 60 % (con 3 intentos o más), o cualquier caso sin clasificar (decisión R-1).
  - VERDE: aprobación de 70 % o más (con 3 intentos o más), o menos de 3 intentos (decisión R-2: no se calculan porcentajes).
  - El cálculo se procesa de forma periódica, cada 5 minutos o menos.
- **Validaciones:**
  - Los datos necesarios deben estar disponibles (actividad y desafíos); las vidas solo con el flag encendido.
- **Datos obligatorios:** curso-cohorte del alumno, indicadores de actividad y rendimiento
- **Performance:** el cálculo corre en segundo plano y es idempotente.
- **Seguridad:** no aplica (cálculo interno; el acceso se controla en HU12).
- **Accesibilidad:** no aplica (cálculo).
- **Otros:** los umbrales viven en configuración tipada (`reporting.risk.*`); el resultado alimenta el panel del docente.

## Criterios de Aceptación
- **CA1:** Se arma el read model de resumen por curso-cohorte.
- **CA2:** Se calcula el estado de riesgo de cada alumno con la regla y las decisiones R-1 y R-2.
- **CA3:** El cálculo se actualiza periódicamente con los datos nuevos.
- **CA4:** Los 12 casos de borde (límites de 10/11 y 4/5 días, 39/40, 60/61 y 69/70 %) tienen test.

## BDD (mínimo 3 escenarios)

**Característica:** Cálculo de riesgo por cohorte

**Escenario 1 — Alumno en riesgo alto**
- **Dado** que un alumno lleva 14 días sin actividad y tiene 40 % de aprobación
- **Cuando** se procesa la foto analítica
- **Entonces** el alumno queda con estado ROJO

**Escenario 2 — Alumno en riesgo medio**
- **Dado** que un alumno tiene 7 días de inactividad
- **Cuando** se procesa la foto analítica
- **Entonces** el alumno queda con estado AMARILLO

**Escenario 3 — Muestra insuficiente**
- **Dado** que un alumno reprobó su único intento y tiene actividad de hoy
- **Cuando** se procesa la foto analítica
- **Entonces** queda VERDE con el aviso de muestra insuficiente y no ROJO

## Prototipo (Mock API / Swagger)
- Cálculo interno (se expone a través del panel del docente, HU12)

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service
- **Módulos afectados:** read model por cohorte, proyector, cálculo de riesgo
- **Otros equipos:** T02 (padrón), T03 (resultados), T10 (vidas, con flag)
- **Datos / migraciones:** V19 (read model de cohorte)
- **Riesgos:** sin padrón de T02 no se ven los inactivos que nunca actuaron → se informa en pantalla

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Definir interfaces y DTOs compartidos de reportes del Sprint 2 (contratos congelados) | Luciano Paz | Backend, Frontend, Integración | CP1 | Es la tarea S2-00; sin lógica, revisan Máximo y Mateo |
| #303 | [G06] - [BACKEND] - Crear migración Flyway y esquema del Read Model analítico por cohorte | Damián Baigorria | Base de Datos, Backend | CP2 | Usar V19 (V18 la ocupa la release) |
| #304 | [G06] - [BACKEND] - Implementar algoritmo de clasificación de nivel de riesgo | Damián Baigorria | Backend, Análisis | CP3 | Incluir R-1 y R-2 |
| #305 | [G06] - [BACKEND] - Implementar job programado de recálculo periódico | Valentina Maldonado | Backend, Base de Datos, Integración | CP3 | Renombrar: proyector cada 5 minutos o menos |
| #306 | [G06] - [TEST] - Desarrollar tests exhaustivos de partición de equivalencia del algoritmo de riesgo | Regina Cerasulo | Testing, Análisis | CP4 | Usar los 12 casos del plan |
| #307 | [G06] - [DOCUMENTACION] - Documentar reglas de cálculo de riesgo y esquema del Read Model | Mateo Carballo | Documentación, Análisis | CP3 | — |
| #308 | [G06] - [REVISION] - Peer review de modelado analítico y verificación en Taiga | Máximo Cerquatti | Testing | CP3 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Definir interfaces y DTOs compartidos de reportes del Sprint 2 (contratos congelados)
[G06] - [BACKEND] - Crear migración Flyway y esquema del Read Model analítico por cohorte
[G06] - [BACKEND] - Implementar algoritmo de clasificación de nivel de riesgo
[G06] - [BACKEND] - Implementar job programado de recálculo periódico
[G06] - [TEST] - Desarrollar tests exhaustivos de partición de equivalencia del algoritmo de riesgo
[G06] - [DOCUMENTACION] - Documentar reglas de cálculo de riesgo y esquema del Read Model
[G06] - [REVISION] - Peer review de modelado analítico y verificación en Taiga
```
