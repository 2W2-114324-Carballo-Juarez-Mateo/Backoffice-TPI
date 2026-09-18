# Tareas Sprint 1 — Índice (división 10 devs, balanceada por capas)

> Backlog del **Sprint 1** (US-01, US-02, US-03, US-08 + Infra + Frontend) dividido entre **10 integrantes**. Balanceado por **horas**, **código** y **capas**: todos tocan **back + front + test + documentación**.

## Archivos
| Archivo | Contenido |
|---|---|
| **[tareas-sprint1.md](tareas-sprint1.md)** | Plan consolidado: decisiones confirmadas, tareas por US/block y asignaciones |
| **[dev-1.md](dev-1.md)** | Luciano Paz — Infra (scaffolding, PR #0) + Outbox + filtro |
| **[dev-2.md](dev-2.md)** | Mateo Carballo Juarez — Envelope/mapeo + reintentos + HTTP front |
| **[dev-3.md](dev-3.md)** | Damian Gabriel Baigorria — US-01 dominio + auditoría T01 |
| **[dev-4.md](dev-4.md)** | Joaquin Cortez — US-01 endpoints + outbox + Idempotency-Key |
| **[dev-5.md](dev-5.md)** | Julieta Ariadna Disca — Tests resiliencia/duplicados + FE-6 |
| **[dev-6.md](dev-6.md)** | Valentina Maldonado — Consumidores T03/T02 + dedup + FE-5 |
| **[dev-7.md](dev-7.md)** | Maximo Cerquatti — Tests filtro/ingesta + rol vía T01 |
| **[dev-8.md](dev-8.md)** | Regina Loreta Cerasulo — Cliente T01 + ErrorApi + guards |
| **[dev-9.md](dev-9.md)** | Bruno Gianoli — Headers Kafka + conectar pantalla parámetros |
| **[dev-10.md](dev-10.md)** | Ana Paula Ducart — MSII: docs, diagramas, contratos (solo no-código) |

## Resumen de carga (10 devs)

| Dev | Integrante | Total aprox. | Capacidad |
|---|---|---:|---:|
| Dev 1 | Luciano Paz | ~26 h | 39,4h |
| Dev 2 | Mateo Carballo Juarez | ~27 h | 41,5h |
| Dev 3 | Damian Gabriel Baigorria | ~24 h | 31,5h |
| Dev 4 | Joaquin Cortez | ~21 h | 27,7h |
| Dev 5 | Julieta Ariadna Disca | ~34 h | 51,5h |
| Dev 6 | Valentina Maldonado | ~25 h | 35,3h |
| Dev 7 | Maximo Cerquatti | ~30 h | 46,4h |
| Dev 8 | Regina Loreta Cerasulo | ~24 h | 35,3h |
| Dev 9 | Bruno Gianoli | ~21 h | 28,4h |
| Dev 10 | Ana Paula Ducart | ~18 h | 25,2h |

**Total:** ~250 h · **Capacidad del grupo (10):** 362 h (con ceremonias y % de dedicación) → margen ~110 h.

## Decisiones confirmadas en planning
- **Repo de entrega:** `2026-P4-BE/tpi-backoffice` (mono-módulo, Boot 4). **1 datasource + 2 esquemas** (`administration`/`reporting`).
- **Mensajería:** formato oficial `EventoDTO` (5 campos + headers Kafka) **PENDIENTE** de confirmar con Usuarios/cátedra → se mantiene el envelope actual.
- **Frontend canónico:** **`/FE` del Demo** (`TPI---Backoffice-Demo-/FE/`).
- **US-04 → Sprint 2.** · **US-08 acotada a T03 + T02** (+T08 REST). · **US-03** agrega tareas de rol vía T01.
- **Scaffolding (PR #0): Luciano (Dev 1).** · **Nadie testea lo suyo.** · **Ana Paula (Dev 10):** solo MSII.
- **DoD Nivel 1:** RLS acotado a historias con datos por `course_id`.

## Notas de la división
- Balanceada por **capas** (BACK + FRONT + TEST + DOC para cada uno), no solo por horas.
- **US-02 T1** partida en **T1a** (migración outbox + 2 esquemas, PR #1 Día 2) + **T1b** (publisher).
- **US-08 T2a** = consumidores T03 (challenge.events) + T02 (course.events); no se consumen temas sin contrato.
- Revisiones cruzadas al final (no bloquean arranques); cada uno revisa trabajo **de otro**.

## Referencias obligatorias
- **Auditoría:** `plan/AUDITORIA-SPRINT1.md` · **DoD:** `plan/sprint0/Sprint0-Propuesta.md` §2.
- **RULES.md / SKILLS.md:** raíz `Repositorio/TPI---Backoffice-Demo-/`.
- **Contratos:** `plan/CONTRATOS.md` · **Parámetros:** `plan/PARAMETROS.md` · **Solicitudes:** `plan/solicitudes/`.
- **Camino previo:** `plan/sprint1/tareas divididas (luciano).md` · `plan/sprint1/CAMBIOS-SPRINT1.md` · `plan/sprint1/PROPUESTA-B-CAPAS.md`.

## Registro de trabajo / trazabilidad (feedback de la IA)

Al final de **cada `dev-N.md`** se completa un **"Registro de trabajo"** con **un bloque por tarea asignada** (estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda). Práctica propia del equipo: **no** forma parte del DoD.