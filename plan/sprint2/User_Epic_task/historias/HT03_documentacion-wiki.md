# HT03 — Documentación de diseño: wiki y diagramas

> Épica: [Parámetros Globales](../epicas/E02_parametros-globales.md) · Taiga: #1631 · Acción: **Reabrir, mover al Sprint 2 y renombrar a "Documentación de diseño: wiki y diagramas"** · Estado actual: Existente (Sprint 1, cerrada)

## Descripción
**Como** Equipo de desarrollo del Backoffice
**Quiero** documentar el Backoffice en la wiki de Taiga con la plantilla de la cátedra, con diagramas y ejemplos reales
**Para** que cualquier persona (docente, compañero o revisor) entienda qué hace el Backoffice y cómo se integra sin leer el código

## Notas / Observaciones

- **Reglas de negocio:**
  - Las páginas se nombran "G06 - TEMA" y siguen la plantilla: descripción, historias, diagramas, explicación, observaciones y endpoints.
  - Los diagramas van en el orden DER, BPMN, clases, estados, secuencias y microservicios, con enlace editable en Draw.io.
  - Cada dueño de historia pasa a Ana tablas y ejemplos reales de request y response y revisa su sección.
  - Ana trabaja en Taiga, wiki y Draw.io; lo que vive en el repositorio lo escribe cada dueño en su PR.
- **Validaciones:**
  - Los ejemplos de endpoints son reales y sin datos personales.
  - El DER coincide con las migraciones vigentes.
- **Datos obligatorios:** páginas de wiki, diagramas, ejemplos
- **Performance:** no aplica.
- **Seguridad:** ningún ejemplo incluye secretos.
- **Accesibilidad:** los diagramas son legibles y cada uno tiene su explicación.
- **Otros:** hoy G06 no tiene ninguna página de wiki.

## Criterios de Aceptación
- **CA1:** Existen las páginas de wiki de G06 (parámetros y administración, gobernanza LLM y contratos de lectura, reportes y panel docente).
- **CA2:** Cada página tiene sus diagramas con enlace editable y su explicación.
- **CA3:** Taiga no tiene estados incoherentes y el Sprint 2 está cargado.
- **CA4:** Existe el guion de la demo con su checklist.

## BDD (mínimo 3 escenarios)

**Característica:** Documentación del Backoffice

**Escenario 1 — Página de wiki completa**
- **Dado** que el dueño de la historia entregó sus ejemplos reales
- **Cuando** Ana arma la página
- **Entonces** la página tiene todas las secciones de la plantilla

**Escenario 2 — Diagrama desactualizado**
- **Dado** que cambió una migración
- **Cuando** se revisa el DER
- **Entonces** el diagrama se actualiza antes de cerrar la página

**Escenario 3 — Taiga coherente**
- **Dado** que hay historias cerradas con tareas abiertas
- **Cuando** se revisa el tablero
- **Entonces** los estados quedan corregidos

## Prototipo (Mock API / Swagger)
- Sin endpoint (documentación): wiki de Taiga y Draw.io

## Estimación / Prioridad
- **Puntos (Fibonacci):** 3
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** Taiga, Draw.io
- **Módulos afectados:** wiki, diagramas, guion de la demo
- **Otros equipos:** todos los dueños de historia (insumos)
- **Datos / migraciones:** ninguno
- **Riesgos:** los insumos llegan tarde → las secciones se cierran al final del checkpoint

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [DOCUMENTACION] - Dejar Taiga coherente desde el CP0 y cargar el Sprint 2 | Ana Ducart | Gestión, Documentación | CP0 | — |
| Nueva | [G06] - [DOCUMENTACION] - Crear las páginas de wiki de G06 del Sprint 1 con la plantilla de la cátedra | Ana Ducart | Documentación, Diseño / UX-UI | CP3 | — |
| Nueva | [G06] - [DOCUMENTACION] - Diagramar la secuencia del cambio de parámetro | Ana Ducart | Documentación, Diseño / UX-UI | CP3 | — |
| Nueva | [G06] - [DOCUMENTACION] - Diagramar las fuentes de datos hacia los reportes | Ana Ducart | Documentación, Diseño / UX-UI | CP2 | — |
| Nueva | [G06] - [DOCUMENTACION] - Redactar el guion de la demo del Sprint 2 y el checklist E2E | Ana Ducart | Documentación, Gestión | CP5 | — |
| Nueva | [G06] - [DOCUMENTACION] - Subir por PR la documentación del Sprint 1 (plan MVP, auditoría Skill Hub y DEVELOPMENT.md) | Luciano Paz | Documentación, Gestión | CP0 | — |

### Texto para copiar en Taiga

```text
[G06] - [DOCUMENTACION] - Dejar Taiga coherente desde el CP0 y cargar el Sprint 2
[G06] - [DOCUMENTACION] - Crear las páginas de wiki de G06 del Sprint 1 con la plantilla de la cátedra
[G06] - [DOCUMENTACION] - Diagramar la secuencia del cambio de parámetro
[G06] - [DOCUMENTACION] - Diagramar las fuentes de datos hacia los reportes
[G06] - [DOCUMENTACION] - Redactar el guion de la demo del Sprint 2 y el checklist E2E
[G06] - [DOCUMENTACION] - Subir por PR la documentación del Sprint 1 (plan MVP, auditoría Skill Hub y DEVELOPMENT.md)
```
