# Tareas Sprint 1 — Carga en Taiga

> **Fuente de verdad:** `plan/tareas.md` · **Convención:** `[G06] - [ROL] - [Descripción]`
> **Criterio:** Solo tareas BACKEND / TEST / DOCUMENTACION / REVISION. Las FRONTEND van a TH-03 (fuera del sprint de Back).

---

## US-02 · Propagación del cambio de parámetro (Outbox + Kafka + Caché TTL)

> **Rama:** `feature/foundation-outbox` · **PR #1 · Merge: Día 3** · **Flyway:** `admin_db → V1`
> **Depende de:** Nada (es fundacional).

| # | Tarea (nombre para Taiga) | Rol | Responsable | Horas |
|---|---|---|---|---|
| 1 | [G06] - [BACKEND] - Crear tabla outbox_events con migración Flyway e implementar publisher programado | BACKEND | **Dev 1** | 8 |
| 2 | [G06] - [BACKEND] - Definir envelope estándar del evento GlobalConfigurationChanged | BACKEND | **Dev 1** | 4 |
| 3 | [G06] - [BACKEND] - Implementar reintentos con backoff exponencial y Dead Letter Topic | BACKEND | **Dev 2** | 6 |
| 4 | [G06] - [BACKEND] - Implementar lógica de idempotencia por eventId y versión en consumidor de referencia | BACKEND | **Dev 2** | 4 |
| 5 | [G06] - [TEST] - Desarrollar test de integración del ciclo completo Outbox → Kafka → consumo | TEST | **Dev 2** | 8 |
| 6 | [G06] - [TEST] - Validar resiliencia ante caída del broker de Kafka | TEST | **Dev 2** | 6 |
| 7 | [G06] - [TEST] - Validar descarte de eventos duplicados y de versión anterior | TEST | **Dev 5** | 4 |
| 8 | [G06] - [DOCUMENTACION] - Documentar envelope estándar, catálogo de topics y contrato del consumidor | DOCUMENTACION | **Dev 1** | 4 |
| 9 | [G06] - [REVISION] - Peer review de concurrencia y transaccionalidad, verificación en Taiga | REVISION | **Dev 5** | 3 |

**Subtotal US-02:** 47h · Dev 1 = 16h · Dev 2 = 24h · Dev 5 = 7h

---

## US-01 · Modificación y versionado de parámetros globales

> **Rama:** `feature/us-01-parametros` · **PR #4 · Merge: Día 7** · **Flyway:** `admin_db → V2`
> **Depende de:** US-02 (tabla outbox en main).

| # | Tarea (nombre para Taiga) | Rol | Responsable | Horas |
|---|---|---|---|---|
| 1 | [G06] - [BACKEND] - Crear migración Flyway y entidad GlobalParameter con historial de versiones | BACKEND | **Dev 3** | 6 |
| 2 | [G06] - [BACKEND] - Implementar caso de uso UpdateParameterCommand con versionado y vigencia no retroactiva | BACKEND | **Dev 3** | 6 |
| 3 | [G06] - [BACKEND] - Implementar endpoints REST GET/PUT de parámetros con autorización por rol | BACKEND | **Dev 4** | 6 |
| 4 | [G06] - [BACKEND] - Persistir registro en outbox_events dentro de la misma transacción del cambio | BACKEND | **Dev 4** | 6 |
| 5 | [G06] - [FRONTEND] - Diseñar pantalla de catálogo y formulario reactivo de edición de parámetros | FRONTEND | *(TH-03)* | 8 |
| 6 | [G06] - [FRONTEND] - Integrar servicio HTTP con BFF/Gateway y control visual de roles | FRONTEND | *(TH-03)* | 4 |
| 7 | [G06] - [TEST] - Desarrollar tests unitarios de dominio: versionado, idempotencia y vigencia | TEST | **Dev 3** | 6 |
| 8 | [G06] - [TEST] - Desarrollar tests de integración con Testcontainers (PostgreSQL) | TEST | **Dev 4** | 6 |
| 9 | [G06] - [DOCUMENTACION] - Congelar contrato OpenAPI 3 y diseñar diagrama de secuencia | DOCUMENTACION | **Dev 4** | 4 |
| 10 | [G06] - [REVISION] - Peer review de PR, validación de DoD Nivel 1 y cierre en Taiga | REVISION | **Dev 6** | 3 |

**Subtotal US-01 (sin frontend):** 43h · Dev 3 = 18h · Dev 4 = 22h · Dev 6 = 3h
**Frontend diferido a TH-03:** T5 (8h) + T6 (4h) = 12h

---

## US-03 · Consola de gestión administrativa e integración de identidades (Tema 01)

> **Rama:** `feature/us-03-gateway-auth` · **PR #2 · Merge: Día 5** · **Flyway:** Ninguno
> **Depende de:** Nada (sin BD propia). Puede mergear en cualquier momento.

| # | Tarea (nombre para Taiga) | Rol | Responsable | Horas |
|---|---|---|---|---|
| 1 | [G06] - [BACKEND] - Implementar filtro de seguridad e inspección de headers propagados del Gateway (X-User-Roles, X-User-Id) | BACKEND | **Dev 5** | 4 |
| 2 | [G06] - [BACKEND] - Implementar cliente HTTP hacia Tema 01 vía Gateway para operaciones administrativas y auditoría delegada | BACKEND | **Dev 6** | 6 |
| 3 | [G06] - [FRONTEND] - Diseñar consola SPA para gestión de roles y cuentas consumiendo APIs de Tema 01 vía Gateway | FRONTEND | *(TH-03)* | 8 |
| 4 | [G06] - [FRONTEND] - Implementar salvaguardas visuales y control de sesión activa (bloqueo de auto-revocación) | FRONTEND | *(TH-03)* | 4 |
| 5 | [G06] - [TEST] - Desarrollar tests de integración del filtro de seguridad y autorización por headers del Gateway | TEST | **Dev 5** | 6 |
| 6 | [G06] - [TEST] - Desarrollar tests de integración y renderizado de componentes de la consola administrativa en Angular | FRONTEND/TEST | *(TH-03)* | 6 |
| 7 | [G06] - [DOCUMENTACION] - Especificar matriz de delegación de identidades con Tema 01 y contrato de borde | DOCUMENTACION | **Dev 6** | 4 |
| 8 | [G06] - [REVISION] - Auditoría de fronteras de microservicios, seguridad en PR y control en Taiga | REVISION | **Dev 6** | 3 |

**Subtotal US-03 (sin frontend):** 23h · Dev 5 = 10h · Dev 6 = 13h
**Frontend diferido a TH-03:** T3 (8h) + T4 (4h) + T6 (6h) = 18h

---

## US-04 · Registro de proveedores y modelos de IA

> **Rama:** `feature/us-04-modelos-ia` · **PR #5 · Merge: Día 8** · **Flyway:** `admin_db → V3`
> **Depende de:** PR #1 (Dupla 1 → Outbox funcional en main).

| # | Tarea (nombre para Taiga) | Rol | Responsable | Horas |
|---|---|---|---|---|
| 1 | [G06] - [BACKEND] - Crear migración Flyway y entidades ModelProvider y LlmModel con cifrado de API Keys | BACKEND | **Dev 7** | 6 |
| 2 | [G06] - [BACKEND] - Implementar endpoint de registro de proveedores y catálogo con enmascaramiento | BACKEND | **Dev 8** | 6 |
| 3 | [G06] - [BACKEND] - Implementar regla de estado inicial PENDING_REVIEW con bloqueo de activación | BACKEND | **Dev 7** | 4 |
| 4 | [G06] - [FRONTEND] - Diseñar pantalla de gestión de proveedores y catálogo de modelos | FRONTEND | *(TH-03)* | 8 |
| 5 | [G06] - [TEST] - Desarrollar tests de seguridad: cifrado en BD y enmascaramiento en respuestas | TEST | **Dev 8** | 6 |
| 6 | [G06] - [TEST] - Validar bloqueo de activación para modelos en PENDING_REVIEW | TEST | **Dev 8** | 4 |
| 7 | [G06] - [DOCUMENTACION] - Documentar endpoints de proveedores/modelos en OpenAPI y actualizar SDD | DOCUMENTACION | **Dev 7** | 3 |
| 8 | [G06] - [REVISION] - Peer review de seguridad de credenciales y control en Taiga | REVISION | **Dev 6** | 3 |

**Subtotal US-04 (sin frontend):** 32h · Dev 7 = 13h · Dev 8 = 16h · Dev 6 = 3h
**Frontend diferido a TH-03:** T4 (8h)

---

## US-08 · Ingesta de datos de los temas con deduplicación

> **Rama:** `feature/us-08-ingesta-kafka` · **PR #3 · Merge: Día 6** · **Flyway:** `reporting_db → V1`
> **Depende de:** Nada de admin_db. Microservicio y BD 100% aislados.

| # | Tarea (nombre para Taiga) | Rol | Responsable | Horas |
|---|---|---|---|---|
| 1 | [G06] - [BACKEND] - Crear migración Flyway y tabla de deduplicación ProcessedEvent | BACKEND | **Dev 9** | 4 |
| 2 | [G06] - [BACKEND] - Implementar consumidores de Kafka para los 6 temas proveedores | BACKEND | **Dev 9** | 12 |
| 3 | [G06] - [BACKEND] - Implementar deduplicación por eventId en cada consumidor | BACKEND | **Dev 9** | 4 |
| 4 | [G06] - [BACKEND] - Configurar Dead Letter Topic para eventos malformados | BACKEND | **Dev 10** | 4 |
| 5 | [G06] - [TEST] - Desarrollar tests de integración: ingesta, deduplicación y DLT | TEST | **Dev 10** | 8 |
| 6 | [G06] - [DOCUMENTACION] - Mapear contratos de lectura y esquemas JSON de los 6 temas | DOCUMENTACION | **Dev 10** | 6 |
| 7 | [G06] - [REVISION] - Peer review de consumidores y control en Taiga | REVISION | **Dev 8** | 3 |

**Subtotal US-08:** 41h · Dev 9 = 20h · Dev 10 = 18h · Dev 8 = 3h

---

## Tarea Transversal (Sprint 0 / Infraestructura)

> Esta tarea NO está en tareas.md pero es requisito previo para que las demás arranquen.
> Decidir si se carga como tarea del Sprint 1 o como cierre de Sprint 0.

| # | Tarea (nombre para Taiga) | Rol | Responsable | Horas |
|---|---|---|---|---|
| 0 | [G06] - [BACKEND] - Scaffolding Maven multi-módulo + Docker Compose (PostgreSQL, Kafka, Eureka) | INFRA | **Dev 1** | 6 |

---

## Resumen de Carga por Integrante (Corregido)

| Dev | Dupla | Tareas asignadas | Total horas |
|---|---|---|---|
| **Dev 1** | 🔵 1 | US-02 T1 (8h) + US-02 T2 (4h) + US-02 T8 (4h) + Scaffolding (6h) | **22h** |
| **Dev 2** | 🔵 1 | US-02 T3 (6h) + US-02 T4 (4h) + US-02 T5 (8h) + US-02 T6 (6h) | **24h** |
| **Dev 3** | 🔷 2 | US-01 T1 (6h) + US-01 T2 (6h) + US-01 T7 (6h) | **18h** |
| **Dev 4** | 🔷 2 | US-01 T3 (6h) + US-01 T4 (6h) + US-01 T8 (6h) + US-01 T9 (4h) | **22h** |
| **Dev 5** | 🟣 3 | US-03 T1 (4h) + US-03 T5 (6h) + US-02 T7 (4h) + US-02 T9 (3h) | **17h** |
| **Dev 6** | 🟣 3 | US-03 T2 (6h) + US-03 T7 (4h) + US-03 T8 (3h) + US-01 T10 (3h) + US-04 T8 (3h) | **19h** |
| **Dev 7** | 🟡 4 | US-04 T1 (6h) + US-04 T3 (4h) + US-04 T7 (3h) | **13h** |
| **Dev 8** | 🟡 4 | US-04 T2 (6h) + US-04 T5 (6h) + US-04 T6 (4h) + US-08 T7 (3h) | **19h** |
| **Dev 9** | 🔴 5 | US-08 T1 (4h) + US-08 T2 (12h) + US-08 T3 (4h) | **20h** |
| **Dev 10** | 🔴 5 | US-08 T4 (4h) + US-08 T5 (8h) + US-08 T6 (6h) | **18h** |

**Total Sprint 1 (backend):** 192h + 6h scaffolding = **198h**

### Desbalances visibles

| Dev | Horas | Observación |
|---|---|---|
| **Dev 7** | **13h** | El más bajo. US-04 tiene pocas tareas backend (3 de 8; la T4 es frontend). Opciones: asignarle una tarea de Sprint 0 pendiente, o adelantar US-05 T1 si el sprint lo permite. |
| **Dev 5** | **17h** | Segundo más bajo. Compensado con 2 tareas de review que requieren lectura profunda. |
| **Dev 2** | **24h** | El más alto. US-02 es la historia más densa en testing de resiliencia. Aceptable porque son tests similares entre sí (mismo contexto Testcontainers). |

> ⚠️ **Nota para Taiga:** Las tareas de FRONTEND (marcadas como *TH-03*) se cargan en las User Stories correspondientes pero **no se asignan** a ningún dev del sprint de Back. Quedan en estado "New" para el sprint de Front.
