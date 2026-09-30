# HT04 — Frontend: conexión al backend (capa HTTP, guards y feedback)

> Épica: [Parámetros Globales](../epicas/E02_parametros-globales.md) · Taiga: #1632 · Acción: **Mover al Sprint 2** · Estado actual: Existente (Sprint 1)

## Descripción
**Como** ADMIN, GESTOR o PROFESSOR
**Quiero** que las pantallas usen las rutas `/api/backoffice`, respeten mi rol y muestren el estado de mi sesión
**Para** trabajar solo con lo que mi rol permite y con datos reales

## Notas / Observaciones

- **Reglas de negocio:**
  - Las pantallas de parámetros, historial, ingesta y contratos migran de `/api/administration` y `/api/reports` a `/api/backoffice`.
  - Los alias viejos del backend no se borran en este sprint.
  - El dashboard oculta los accesos que GESTOR y PROFESSOR no pueden usar.
  - El estado de 2FA y de sesión depende de lo que exponga T01.
- **Validaciones:**
  - Ninguna llamada nueva contra `/api/administration` o `/api/reports`.
  - Los archivos protegidos del repositorio del frontend no se tocan.
- **Datos obligatorios:** rol, ruta, estado de 2FA
- **Performance:** no se agregan llamadas en el arranque.
- **Seguridad:** los guards son solo una capa de experiencia: la autorización real es del backend.
- **Accesibilidad:** los estados de carga y error son accesibles y navegables por teclado.
- **Otros:** las tareas de guards y badge ya están escritas; solo falta abrir sus PRs.

## Criterios de Aceptación
- **CA1:** Las partes 01, 02, 04 y 06 usan `/api/backoffice` y se retiró el parche del proxy.
- **CA2:** GESTOR y PROFESSOR no ven accesos no permitidos en el dashboard.
- **CA3:** Hay specs de guards y de la vista de solo lectura para los tres roles.
- **CA4:** La pantalla de administración muestra el estado de 2FA y sesión (si T01 lo expone).

## BDD (mínimo 3 escenarios)

**Característica:** Conexión del frontend al backend

**Escenario 1 — Ruta migrada**
- **Dado** que la pantalla de parámetros usaba `/api/administration`
- **Cuando** carga la pantalla
- **Entonces** llama a `/api/backoffice/parameters`

**Escenario 2 — Dashboard por rol**
- **Dado** que entra un PROFESSOR
- **Cuando** abre el dashboard
- **Entonces** no ve los accesos de administración

**Escenario 3 — Estado de 2FA**
- **Dado** que T01 expone el estado de 2FA
- **Cuando** el ADMIN abre la pantalla de administración
- **Entonces** ve el estado de 2FA y de sesión

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/parameters
- GET /api/backoffice/reports/health/freshness

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** frontend, backoffice-service
- **Módulos afectados:** capa HTTP, guards, dashboard
- **Otros equipos:** T01 (2FA y sesión)
- **Datos / migraciones:** ninguno
- **Riesgos:** el repositorio del frontend es compartido → no tocar `angular.json`, `package*.json` ni `tsconfig*`

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [FRONTEND] - Migrar las pantallas a /api/backoffice y retirar el parche del proxy | Luciano Paz | Frontend, Integración | CP1 | — |
| Nueva | [G06] - [FRONTEND] - Ocultar en el dashboard los accesos no permitidos a GESTOR y PROFESSOR | Valentina Maldonado | Frontend, Seguridad | CP3 | — |
| #1657 | [G06] - [FRONTEND] - Pantalla de administración: estado de 2FA y sesión | Luciano Paz | Frontend, Seguridad | CP3 | Pasa de Regina a Luciano; condicionada a T01 |
| Nueva | [G06] - [TEST] - Desarrollar specs de guards y de la vista de solo lectura (ADMIN, GESTOR y PROFESSOR) | Mateo Carballo | Testing, Frontend, Seguridad | CP4 | — |
| Nueva | [G06] - [REVISION] - Peer review de guards, migración de rutas, badge y 2FA | Joaquín Cortez | Testing | CP4 | — |
| Nueva | [G06] - [BACKEND] - Cerrar la rama del frontend superada (fix/admin-export-service-spec) | Mateo Carballo | Gestión, DevOps | CP0 | — |

### Texto para copiar en Taiga

```text
[G06] - [FRONTEND] - Migrar las pantallas a /api/backoffice y retirar el parche del proxy
[G06] - [FRONTEND] - Ocultar en el dashboard los accesos no permitidos a GESTOR y PROFESSOR
[G06] - [FRONTEND] - Pantalla de administración: estado de 2FA y sesión
[G06] - [TEST] - Desarrollar specs de guards y de la vista de solo lectura (ADMIN, GESTOR y PROFESSOR)
[G06] - [REVISION] - Peer review de guards, migración de rutas, badge y 2FA
[G06] - [BACKEND] - Cerrar la rama del frontend superada (fix/admin-export-service-spec)
```
