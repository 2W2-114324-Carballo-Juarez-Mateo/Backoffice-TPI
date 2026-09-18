# Tareas Sprint 1 — Backlog del sprint ya dividido (9 devs)

> **Alcance:** solo las historias priorizadas para este Sprint 1 → **US-01, US-02, US-03, US-04, US-08** + **Infra** (scaffolding).
> **Flujo de ramas:** cada dev trabaja en `feature/<tema>` desde **`develop`** → **PR a `develop`** → `main` solo al final (detalle en `RULES.md` del repo de trabajo).
> **Fuente:** `plan/tareas.md` (backlog general) + división de Luciano (`plan/sprint1/tareas divididas (luciano).md`).
> **Convención:** `[G06] - [ROL] - [Descripción]` · **Estimación:** historia = SP (Fibonacci), tarea = horas.
> **Solo Back:** las tareas FRONTEND van a **TH-03** (fuera de la capacidad del sprint de Back).
>
> **Cambios de esta división (respecto al backlog general):**
> - **US-02 T1** se parte en **T1a** (migración `outbox_message`, merge Día 1) + **T1b** (publisher programado).
> - **US-08 T2** se parte en **T2a** (consumidores T02/T05/T03) + **T2b** (T06/T07/T10).
> - Se elimina la dependencia **US-04 → outbox** (US-04 no publica eventos).
> - Pasamos de **10 a 9 devs** (el Dev 10 no codifica).

## Resumen de carga (balanceada)

| Dev | Integrante | Tareas | Total | Código (BE+TEST) |
|---|---|---|---:|---:|
| **Dev 1** | Luciano Paz | US-02 T1a + T1b + T8 · Infra | 18 | 14 |
| **Dev 2** | Mateo Carballo Juarez | US-02 T2 + T3 + T4 + T5 + T9 | 25 | 22 |
| **Dev 3** | Damian Gabriel Baigorria | US-01 T1 + T2 + T7 + T10 | 21 | 18 |
| **Dev 4** | Joaquin Cortez | US-01 T3 + T4 + T8 + T9 | 22 | 18 |
| **Dev 5** | Julieta Ariadna Disca | US-02 T6 + T7 · US-08 T1 + T4 · US-03 T8 | 21 | 18 |
| **Dev 6** | Valentina Maldonado | US-08 T2a + T2b + T3 + T6 | 22 | 16 |
| **Dev 7** | Maximo Cerquatti | US-08 T5 + T7 · US-03 T1 + T5 | 21 | 18 |
| **Dev 8** | Regina Loreta Cerasulo | US-03 T2 + T7 · US-04 T1 + T3 | 20 | 16 |
| **Dev 9** | Bruno Gianoli | US-04 T2 + T5 + T6 + T7 + T8 | 22 | 16 |

**Total:** 192h · rango 18–25h totales · 14–22h de código por dev.
> **Ajuste:** US-02 **T2** (envelope) se movió de Dev 1 a **Dev 2 (Mateo)** como primera tarea de arranque (Día 1).

---

## US-02 · Propagación del cambio de parámetro (Outbox + Kafka + Caché TTL)

> **Rama:** `feature/foundation-outbox` · **PR a develop #1 · merge a develop: Día 3** · **Flyway:** `admin_db → V1` · **Depende de:** Nada (fundacional).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1a | Crear migración Flyway de la tabla `outbox_message` | BACKEND | Dev 1 | 3 |
| T1b | Implementar publisher programado (Outbox → Kafka) | BACKEND | Dev 1 | 5 |
| T2 | Definir envelope estándar del evento `GlobalConfigurationChanged` | BACKEND | Dev 2 | 4 |
| T3 | Implementar reintentos con backoff exponencial y Dead Letter Topic | BACKEND | Dev 2 | 6 |
| T4 | Idempotencia por `eventId` y versión en consumidor de referencia | BACKEND | Dev 2 | 4 |
| T5 | Test de integración del ciclo completo Outbox → Kafka → consumo | TEST | Dev 2 | 8 |
| T6 | Validar resiliencia ante caída del broker de Kafka | TEST | Dev 5 | 6 |
| T7 | Validar descarte de eventos duplicados y de versión anterior | TEST | Dev 5 | 4 |
| T8 | Documentar envelope, catálogo de topics y contrato del consumidor | DOCUMENTACION | Dev 1 | 4 |
| T9 | Peer review de concurrencia y transaccionalidad | REVISION | Dev 2 | 3 |

**Subtotal:** 47h.

---

## US-01 · Modificación y versionado de parámetros globales

> **Rama:** `feature/us-01-parametros` · **PR a develop #4 · merge a develop: Día 7** · **Flyway:** `admin_db → V2` · **Depende de:** US-02 T1a (tabla `outbox_message`).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1 | Crear migración Flyway y entidad `GlobalParameter` con historial de versiones | BACKEND | Dev 3 | 6 |
| T2 | Caso de uso `UpdateParameterCommand` con versionado y vigencia no retroactiva | BACKEND | Dev 3 | 6 |
| T3 | Endpoints REST GET/PUT de parámetros con autorización por rol | BACKEND | Dev 4 | 6 |
| T4 | Persistir el registro en `outbox_message` dentro de la misma transacción | BACKEND | Dev 4 | 6 |
| T7 | Tests unitarios de dominio: versionado, idempotencia y vigencia | TEST | Dev 3 | 6 |
| T8 | Tests de integración con Testcontainers (PostgreSQL) | TEST | Dev 4 | 6 |
| T9 | Congelar contrato OpenAPI 3 y diseñar diagrama de secuencia | DOCUMENTACION | Dev 4 | 4 |
| T10 | Peer review de PR, validación de DoD Nivel 1 y cierre en Taiga | REVISION | Dev 3 | 3 |

**Subtotal:** 43h · **Frontend diferido a TH-03:** T5 (8h) + T6 (4h).

---

## US-03 · Consola de gestión administrativa e integración de identidades (Tema 01)

> **Rama:** `feature/us-03-gateway-auth` · **PR a develop #2 · merge a develop: Día 5** · **Flyway:** Ninguno · **Depende de:** Nada.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1 | Filtro de seguridad e inspección de headers del Gateway (`X-User-Roles`, `X-User-Id`) | BACKEND | Dev 7 | 4 |
| T2 | Cliente HTTP hacia Tema 01 vía Gateway (admin + auditoría delegada) | BACKEND | Dev 8 | 6 |
| T5 | Tests de integración del filtro de seguridad y autorización por headers | TEST | Dev 7 | 6 |
| T7 | Especificar matriz de delegación de identidades con Tema 01 y contrato de borde | DOCUMENTACION | Dev 8 | 4 |
| T8 | Auditoría de fronteras de microservicios, seguridad en PR y control en Taiga | REVISION | Dev 5 | 3 |

**Subtotal:** 23h · **Frontend diferido a TH-03:** T3 (8h) + T4 (4h) + T6 (6h).

---

## US-04 · Registro de proveedores y modelos de IA

> **Rama:** `feature/us-04-modelos-ia` · **PR a develop #5 · merge a develop: Día 8** · **Flyway:** `admin_db → V3` · **Depende de:** Nada (se quitó la dependencia de outbox).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1 | Migración Flyway y entidades `ModelProvider` / `LlmModel` con cifrado de API Keys | BACKEND | Dev 8 | 6 |
| T2 | Endpoint de registro de proveedores y catálogo con enmascaramiento | BACKEND | Dev 9 | 6 |
| T3 | Regla de estado inicial `PENDING_REVIEW` con bloqueo de activación | BACKEND | Dev 8 | 4 |
| T5 | Tests de seguridad: cifrado en BD y enmascaramiento en respuestas | TEST | Dev 9 | 6 |
| T6 | Validar bloqueo de activación para modelos en `PENDING_REVIEW` | TEST | Dev 9 | 4 |
| T7 | Documentar endpoints de proveedores/modelos en OpenAPI y actualizar SDD | DOCUMENTACION | Dev 9 | 3 |
| T8 | Peer review de seguridad de credenciales y control en Taiga | REVISION | Dev 9 | 3 |

**Subtotal:** 32h · **Frontend diferido a TH-03:** T4 (8h).

---

## US-08 · Ingesta de datos de los temas con deduplicación

> **Rama:** `feature/us-08-ingesta-kafka` · **PR a develop #3 · merge a develop: Día 6** · **Flyway:** `reporting_db → V1` · **Depende de:** Nada (microservicio y BD aislados).

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T1 | Crear migración Flyway y tabla de deduplicación `ProcessedEvent` | BACKEND | Dev 5 | 4 |
| T2a | Consumidores de Kafka para T02 / T05 / T03 (`challenge.events`) | BACKEND | Dev 6 | 6 |
| T2b | Consumidores de Kafka para T06 / T07 / T10 | BACKEND | Dev 6 | 6 |
| T3 | Deduplicación por `eventId` en cada consumidor | BACKEND | Dev 6 | 4 |
| T4 | Configurar Dead Letter Topic para eventos malformados | BACKEND | Dev 5 | 4 |
| T5 | Tests de integración: ingesta, deduplicación y DLT | TEST | Dev 7 | 8 |
| T6 | Mapear contratos de lectura y esquemas JSON de los 6 temas | DOCUMENTACION | Dev 6 | 6 |
| T7 | Peer review de consumidores y control en Taiga | REVISION | Dev 7 | 3 |

**Subtotal:** 41h.

> **Nota (acuerdos):** **T08 (Banco)** se ingiere por **evento de saldo + REST** (`/api/bank/**` para replay). **T03 (Desafíos)** se consume por `challenge.events` (hecho único). Los consumidores Kafka cubren T02/T05/T03 (T2a) y T06/T07/T10 (T2b).

---

## Infra · Tarea transversal (Sprint 0 / Infraestructura)

> Requisito previo para que el resto arranque.

| # | Tarea | Rol | Dev | Horas |
|---|---|---|---|---:|
| T0 | Scaffolding Maven multi-módulo + Docker Compose (PostgreSQL, Kafka, Eureka) | INFRA | Dev 1 | 6 |

---

> **DoD y convenciones:** ver `plan/sprint0/Sprint0-Propuesta.md` §2 (DoD Nivel 0/1/2) y las reglas/skills en la raíz del repo de trabajo (`RULES.md`, `SKILLS.md`).
> **Contratos y parámetros:** `plan/CONTRATOS.md` y `plan/PARAMETROS.md`.