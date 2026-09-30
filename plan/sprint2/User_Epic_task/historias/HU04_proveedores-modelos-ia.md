# HU04 — Registro de proveedores y modelos de IA

> Épica: [Configuración de Modelos LLM y Golden Set](../epicas/E03_modelos-llm-golden-set.md) · Taiga: #18 · Acción: **Reabrir y mover al Sprint 2** · Estado actual: Existente (Sprint 1, cerrada)

## Descripción
**Como** ADMIN
**Quiero** registrar proveedores con sus credenciales, descubrir sus modelos y probarlos, contra el servicio de evaluación real (T07)
**Para** administrar los proveedores de IA de la plataforma sin exponer sus claves

## Notas / Observaciones

- **Reglas de negocio:**
  - El Backoffice es fachada de T07: los datos y la lógica viven en T07.
  - La key de un proveedor viaja a T07 y nunca se loguea ni se devuelve (se muestra enmascarada).
  - Los endpoints son solo para ADMIN.
- **Validaciones:**
  - La credencial debe indicar proveedor y key.
  - Un error 404, 409 o 503 de T07 se traduce a un mensaje claro y nunca a 500.
- **Datos obligatorios:** proveedor, credencial (solo escritura), modelo
- **Performance:** timeout de 3 segundos hacia T07; reintento solo en lecturas.
- **Seguridad:** solo ADMIN; se propagan `X-Request-Id` y `traceparent`; 401 y 403 de T07 no se convierten en 500.
- **Accesibilidad:** la pantalla 09 se usa por teclado y muestra estados de carga, vacío y error.
- **Otros:** mientras T07 no confirme la ruta queda un stub con flag.

## Criterios de Aceptación
- **CA1:** El ADMIN registra una credencial y la key nunca aparece en respuestas, logs ni excepciones.
- **CA2:** Descubrir y probar modelos funciona contra T07 (o contra el stub con flag).
- **CA3:** La pantalla 09 no usa datos en memoria.
- **CA4:** Un 401 o 403 de T07 llega como tal y no como 500.

## BDD (mínimo 3 escenarios)

**Característica:** Gestión de proveedores LLM

**Escenario 1 — Alta de credencial**
- **Dado** que el ADMIN completa proveedor y key
- **Cuando** guarda la credencial
- **Entonces** la lista muestra la key enmascarada y T07 la recibe

**Escenario 2 — T07 caído**
- **Dado** que T07 no responde
- **Cuando** el ADMIN lista los proveedores
- **Entonces** responde 503 con un mensaje claro

**Escenario 3 — Usuario sin rol**
- **Dado** que un PROFESSOR llama al endpoint de credenciales
- **Cuando** envía la petición
- **Entonces** responde 403

## Prototipo (Mock API / Swagger)
- GET /api/backoffice/llm/providers
- POST /api/backoffice/llm/provider-credentials
- POST /api/backoffice/llm/provider-credentials/{id}/discover-models
- POST /api/backoffice/llm/provider-credentials/{id}/test-model

## Estimación / Prioridad
- **Puntos (Fibonacci):** 5
- **Prioridad (MoSCoW):** Must

## Dependencias / Impactos
- **Servicios / APIs:** backoffice-service, T07
- **Módulos afectados:** cliente HTTP a T07, cliente de proveedores, pantalla 09
- **Otros equipos:** T07 (ruta y autenticación), T01 (token de servicio)
- **Datos / migraciones:** ninguno
- **Riesgos:** T07 no confirma la ruta → se trabaja con WireMock y stub con flag

## Tareas asociadas (para Taiga)

| Taiga | Nombre de la tarea | Responsable | Etiquetas | CP | Nota |
|---|---|---|---|---|---|
| Nueva | [G06] - [BACKEND] - Implementar cliente HTTP hacia T07 con errores problem+json, timeout y reintento solo en lecturas | Máximo Cerquatti | Backend, Integración, Seguridad | CP2 | — |
| Nueva | [G06] - [BACKEND] - Implementar cliente real de proveedores y credenciales sin exponer la key | Regina Cerasulo | Backend, Integración, Seguridad | CP3 | — |
| Nueva | [G06] - [FRONTEND] - Conectar la pantalla de proveedores al backend | Regina Cerasulo | Frontend, Integración | CP3 | — |
| Nueva | [G06] - [TEST] - Desarrollar tests con WireMock del cliente de proveedores (200, 404, 409, 503 y key enmascarada) | Luciano Paz | Testing, Integración | CP3 | — |
| Nueva | [G06] - [REVISION] - Peer review de seguridad de credenciales y del cliente hacia T07 | Bruno Gianoli | Testing, Seguridad | CP3 | — |
| Nueva | [G06] - [DOCUMENTACION] - Documentar en OpenAPI la fachada de proveedores y modelos | Regina Cerasulo | Documentación | CP3 | — |

### Texto para copiar en Taiga

```text
[G06] - [BACKEND] - Implementar cliente HTTP hacia T07 con errores problem+json, timeout y reintento solo en lecturas
[G06] - [BACKEND] - Implementar cliente real de proveedores y credenciales sin exponer la key
[G06] - [FRONTEND] - Conectar la pantalla de proveedores al backend
[G06] - [TEST] - Desarrollar tests con WireMock del cliente de proveedores (200, 404, 409, 503 y key enmascarada)
[G06] - [REVISION] - Peer review de seguridad de credenciales y del cliente hacia T07
[G06] - [DOCUMENTACION] - Documentar en OpenAPI la fachada de proveedores y modelos
```
