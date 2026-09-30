# HT02 — Infra: adopción del scaffolding de cátedra, compose y CI

> Épica: [Parámetros Globales](../epicas/E02_parametros-globales.md) · Taiga: #1630 · Acción: **Reabrir y mover al Sprint 2** · Estado actual: Existente (Sprint 1, cerrada)

## Descripción
**Como** Equipo de desarrollo del Backoffice
**Quiero** cerrar el Sprint 1 en infraestructura: release v1.0.0 en `main`, ramas en orden y borde del Gateway verificado
**Para** arrancar el Sprint 2 sobre `develop` limpio y con el entorno confiable

## Notas / Observaciones

- **Reglas de negocio:**
  - La release v1.0.0 llega a `main` por la rama `release/*`, nunca directo desde `develop`.
  - El back-merge de la release a `develop` trae la migración V18 y va antes que cualquier otra migración.
  - El secreto compartido del Gateway se verifica sin romper el entorno local.
  - Las ramas superadas se cierran sin mergear.
- **Validaciones:**
  - La rama respeta el prefijo `feature/`, `fix/` o `refactor/`.
  - El secreto del Gateway no se versiona.
- **Datos obligatorios:** rama, PR, versión
- **Performance:** no aplica.
- **Seguridad:** el secreto compartido entra por variable de entorno; sin secretos en el código.
- **Accesibilidad:** no aplica.
- **Otros:** las tareas de este sprint reabren la historia de infraestructura del Sprint 1.

## Criterios de Aceptación
- **CA1:** La PR #54 (release v1.0.0) está mergeada en `main` y etiquetada.
- **CA2:** El back-merge (PR #58) está en `develop`.
- **CA3:** Las ramas superadas del backend están cerradas.
- **CA4:** El secreto del Gateway está verificado, o la tarea pasa a "Necesita información" si T01 no responde.

## BDD (mínimo 3 escenarios)

**Característica:** Cierre de infraestructura del Sprint 1

**Escenario 1 — Release a main**
- **Dado** que la PR #54 tiene el CI en verde y una aprobación
- **Cuando** se mergea
- **Entonces** `main` queda en v1.0.0 y se crea el tag

**Escenario 2 — Rama superada**
- **Dado** que una rama quedó reemplazada por otra
- **Cuando** se cierra sin mergear
- **Entonces** no queda ninguna PR abierta con contenido duplicado

**Escenario 3 — Secreto del Gateway**
- **Dado** que T01 acordó el mecanismo
- **Cuando** se configura la variable
- **Entonces** una llamada sin pasar por el Gateway no obtiene rol de ADMIN

## Prototipo (Mock API / Swagger)
- Sin endpoint (infraestructura y ramas)

## Estimación / Prioridad
- **Puntos (Fibonacci):** 3
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** GitHub, CI, Gateway (T01)
- **Módulos afectados:** release, ramas, configuración de seguridad
- **Otros equipos:** T01 (mecanismo del secreto)
- **Datos / migraciones:** V18 (topics del registro de contratos, en la release)
- **Riesgos:** T01 no responde → tarea a "Necesita información"

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Mergear el back-merge de la release v1.0.0 a develop (PR #58, trae la migración V18) | Mateo Carballo | DevOps, Base de Datos | CP0 | — |
| Nueva | [G06] - [BACKEND] - Mergear la release v1.0.0 a main y etiquetar la versión (PR #54) | Damián Baigorria | DevOps, Gestión | CP0 | — |
| Nueva | [G06] - [BACKEND] - Cerrar las ramas superadas del backend (us-02-envelope y contratos-alineados-drive) | Mateo Carballo | Gestión, DevOps | CP0 | — |
| Nueva | [G06] - [BACKEND] - Verificar el secreto compartido del Gateway sin romper el entorno local | Máximo Cerquatti | Backend, Seguridad, Configuración | CP3 | Condicionada a T01 |
| Nueva | [G06] - [REVISION] - Peer review de concurrencia del outbox y del secreto del Gateway | Valentina Maldonado | Testing, Seguridad | CP4 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Mergear el back-merge de la release v1.0.0 a develop (PR #58, trae la migración V18)
[G06] - [BACKEND] - Mergear la release v1.0.0 a main y etiquetar la versión (PR #54)
[G06] - [BACKEND] - Cerrar las ramas superadas del backend (us-02-envelope y contratos-alineados-drive)
[G06] - [BACKEND] - Verificar el secreto compartido del Gateway sin romper el entorno local
[G06] - [REVISION] - Peer review de concurrencia del outbox y del secreto del Gateway
```
