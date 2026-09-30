# HU10 — Control de frescura de los datos y avisos

> Épica: [Contratos de Lectura e Ingesta](../epicas/E06_contratos-lectura-ingesta.md) · Taiga: #28 · Acción: **Mover al Sprint 2 (en "In progress") y actualizar la descripción** · Estado actual: Existente (Sprint 1)

## Descripción
**Como** PROFESOR o ADMIN
**Quiero** ver de cuándo son los datos de cada reporte y saber si están desactualizados
**Para** confiar en lo que veo y no decidir con datos viejos

## Notas / Observaciones

- **Reglas de negocio:**
  - La frescura máxima es de 15 minutos (PAR-23) y se calcula al leer, según las fuentes de cada reporte.
  - Cada reporte informa `asOf`, si está desactualizado y cuáles fuentes son.
  - Una fuente sin eventos se marca desactualizada y se indica cuál es.
- **Validaciones:**
  - El umbral se lee de PAR-23 (15 por defecto).
  - El reporte no falla si una fuente no tiene datos: lo informa.
- **Datos obligatorios:** instante del último evento por fuente, umbral PAR-23
- **Performance:** el cálculo no agrega consultas pesadas al reporte.
- **Seguridad:** la información de frescura respeta el alcance del usuario.
- **Accesibilidad:** el badge de frescura se indica con color y texto y es navegable por teclado.
- **Otros:** el badge del monitor de ingesta ya está re-aplicado con las correcciones de la revisión.

## Criterios de Aceptación
- **CA1:** Todo reporte devuelve la frescura de sus fuentes.
- **CA2:** 14 minutos es "al día", 15 es el límite y 16 es "desactualizado".
- **CA3:** El badge se ve en la cabecera de los reportes.

## BDD (mínimo 3 escenarios)

**Característica:** Frescura de los datos

**Escenario 1 — Datos al día**
- **Dado** que la última novedad de T03 fue hace 10 minutos
- **Cuando** el docente abre su panel
- **Entonces** ve el badge "al día"

**Escenario 2 — Datos desactualizados**
- **Dado** que la última novedad fue hace 16 minutos
- **Cuando** abre el panel
- **Entonces** el badge indica "desactualizado" y la fuente

**Escenario 3 — Fuente sin eventos**
- **Dado** que T02 nunca envió eventos
- **Cuando** abre un reporte
- **Entonces** la fuente se marca sin datos y el reporte igual carga

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/reports/health/freshness → frescura por fuente (ADMIN)
- Todos los reportes incluyen `freshness` en la respuesta

## Estimación / Prioridad
- **Puntos (Fibonacci):** 3
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** reporting-service
- **Módulos afectados:** proveedor de frescura, badge
- **Otros equipos:** ninguno
- **Datos / migraciones:** sin cambios estructurales
- **Riesgos:** una fuente lenta → informarla como desactualizada

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| #291 | [G06] - [FRONTEND] - Diseñar badge de frescura en la cabecera de reportes | Valentina Maldonado | Frontend, Diseño / UX-UI | CP0 | Ya re-aplicado con los fixes: solo falta abrir la PR |
| Nueva | [G06] - [BACKEND] - Implementar cálculo de frescura al leer en cada reporte | Valentina Maldonado | Backend | CP3 | — |
| Nueva | [G06] - [TEST] - Desarrollar tests del cálculo de frescura (14, 15 y 16 minutos) y del proyector de read models | Regina Cerasulo | Testing | CP4 | — |

### Texto para copiar en Taiga

```text
[G06] - [FRONTEND] - Diseñar badge de frescura en la cabecera de reportes
[G06] - [BACKEND] - Implementar cálculo de frescura al leer en cada reporte
[G06] - [TEST] - Desarrollar tests del cálculo de frescura (14, 15 y 16 minutos) y del proyector de read models
```
