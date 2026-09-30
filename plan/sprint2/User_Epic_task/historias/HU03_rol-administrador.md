# HU03 — Asignación y revocación del rol administrador

> Épica: [Administración de la Plataforma](../epicas/E04_administracion-plataforma.md) · Taiga: #182 · Acción: **Mover al Sprint 2 (en "In progress")** · Estado actual: Existente (Sprint 1)

## Descripción
**Como** ADMIN
**Quiero** que solo los administradores accedan a las pantallas de administración y que la auditoría quede configurada
**Para** proteger la gestión de la plataforma y conservar el rastro de cada acción administrativa

## Notas / Observaciones

- **Reglas de negocio:**
  - Las rutas solo para ADMIN están protegidas por guards en el frontend y por `@PreAuthorize` en el backend.
  - Toda acción administrativa se audita y se publica en `identity.audit.events`.
  - GESTOR y PROFESSOR no ven accesos que no pueden usar.
- **Validaciones:**
  - Una URL directa a una pantalla restringida redirige o responde 403.
  - El topic de auditoría viene de una propiedad tipada.
- **Datos obligatorios:** rol del usuario, ruta solicitada
- **Performance:** el guard resuelve sin llamadas extra al servidor.
- **Seguridad:** el rol sale de los headers de confianza del Gateway; el Backoffice no valida firmas.
- **Accesibilidad:** los mensajes de acceso denegado son claros y navegables por teclado.
- **Otros:** la asignación y revocación de cuentas ADMIN las resuelve T01; el Backoffice las consume.

## Criterios de Aceptación
- **CA1:** Un usuario sin rol ADMIN no entra por URL directa a una pantalla de administración.
- **CA2:** Las rutas de T01 en el enrutador no se rompen.
- **CA3:** El topic de auditoría se configura por propiedad.

## BDD (mínimo 3 escenarios)

**Característica:** Acceso a las pantallas de administración

**Escenario 1 — Profesor por URL directa**
- **Dado** que un PROFESSOR conoce la ruta de parámetros
- **Cuando** la abre escribiendo la URL
- **Entonces** el guard lo redirige y no se muestran datos

**Escenario 2 — Administrador**
- **Dado** que un ADMIN inició sesión
- **Cuando** abre cualquier pantalla de administración
- **Entonces** la pantalla carga con normalidad

**Escenario 3 — Auditoría configurable**
- **Dado** que se cambia el topic de auditoría por entorno
- **Cuando** se publica una acción administrativa
- **Entonces** el evento sale al topic configurado

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/audit → auditoría administrativa (ADMIN)

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** backoffice-service, frontend
- **Módulos afectados:** guards, auditoría
- **Otros equipos:** T01 (cuentas y auditoría)
- **Datos / migraciones:** ninguno
- **Riesgos:** el enrutador es compartido con T01 → tocar solo las líneas propias

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| #3512 | [G06] - [FRONTEND] - Implementar guards de rutas de administración | Máximo Cerquatti | Frontend, Seguridad | CP0 | Rama `feature/tema-12-admin-route-guards` ya pusheada: solo falta abrir la PR |
| Nueva | [G06] - [BACKEND] - Pasar el topic de auditoría de constante a propiedad tipada | Máximo Cerquatti | Backend, Configuración | CP3 | — |

### Texto para copiar en Taiga

```text
[G06] - [FRONTEND] - Implementar guards de rutas de administración
[G06] - [BACKEND] - Pasar el topic de auditoría de constante a propiedad tipada
```
