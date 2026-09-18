# Tareas Sprint 1 — Índice (división 10 devs, pareja por capas)

> Backlog del **Sprint 1** (Infra + US-01 + US-02 + US-03 + US-08 acotada + EP-04 + Frontend + Documentación) dividido entre **10 integrantes**. División **pareja**: nadie en una sola capa — todos tocan **BACK o FRONT + TEST + DOC** (o revisión cruzada). **Fuente única de verdad: [`tareas-sprint1.md`](tareas-sprint1.md)**; los `dev-N.md` se derivan de ella.

## Archivos
| Archivo | Contenido |
|---|---|
| **[tareas-sprint1.md](tareas-sprint1.md)** | **Plan canónico**: alcance, capacidad, tareas por US, secuencia de merges, decisiones resueltas |
| **[dev-1.md](dev-1.md)** | Luciano Paz — Infra (scaffolding, PR #0) + Outbox + filtro (BACK/DOC) |
| **[dev-2.md](dev-2.md)** | Mateo Carballo Juarez — Envelope/mapeo + reintentos + HTTP front (BACK/FRONT/DOC) |
| **[dev-3.md](dev-3.md)** | Damian Gabriel Baigorria — US-01 dominio + unit tests + auditoría T01 (BACK/TEST/DOC) |
| **[dev-4.md](dev-4.md)** | Joaquin Cortez — US-01 endpoints + outbox + Idempotency-Key + FE-5 (BACK/DOC/FRONT) |
| **[dev-5.md](dev-5.md)** | Julieta Ariadna Disca — Ingresa + tests resiliencia + revisiones + G4 (BACK/TEST/REV/DOC) |
| **[dev-6.md](dev-6.md)** | Valentina Maldonado — Consumidores T03/T02 + dedup + tests US-01 (BACK/DOC/TEST) |
| **[dev-7.md](dev-7.md)** | Maximo Cerquatti — Tests de integración + rol vía T01 (TEST/REV/BACK) |
| **[dev-8.md](dev-8.md)** | Regina Loreta Cerasulo — Cliente T01 + ErrorApi + guards + G3 (BACK/DOC/FRONT) |
| **[dev-9.md](dev-9.md)** | Bruno Gianoli — Headers Kafka + pantallas conectadas (BACK/REV/FRONT) |
| **[dev-10.md](dev-10.md)** | Ana Paula Ducart — MSII: docs, diagramas, contratos (solo no-código) |

## Resumen de carga (coincide con `tareas-sprint1.md` §6)

| Dev | Integrante | Total | Capacidad | % | Capas |
|---|---|---:|---:|---:|---|
| Dev 1 | Luciano Paz | 24 h | 39,4h | 60,9 % | BACK/INFRA + DOC (camino crítico, liviano a propósito) |
| Dev 2 | Mateo Carballo Juarez | 26 h | 41,5h | 62,7 % | BACK + FRONT + DOC |
| Dev 3 | Damian Gabriel Baigorria | 25 h | 31,5h | 79,4 % | BACK + TEST + DOC |
| Dev 4 | Joaquin Cortez | 23 h | 27,7h | 83,0 % | BACK + DOC + FRONT |
| Dev 5 | Julieta Ariadna Disca | 28 h | 51,5h | 54,4 % | BACK + TEST + REV + DOC |
| Dev 6 | Valentina Maldonado | 22 h | 35,3h | 62,3 % | BACK + TEST + DOC |
| Dev 7 | Maximo Cerquatti | 31 h | 46,4h | 66,8 % | TEST + REV + BACK |
| Dev 8 | Regina Loreta Cerasulo | 22 h | 35,3h | 62,3 % | BACK + DOC + FRONT |
| Dev 9 | Bruno Gianoli | 20 h | 28,4h | 70,4 % | BACK + FRONT + REV |
| Dev 10 | Ana Paula Ducart | 20 h | 25,2h | 79,4 % | DOC (solo MSII) |

**Total: 241 h / 362,1 h = 66,6 %.**

## Decisiones resueltas en planning (ver `tareas-sprint1.md` §14)
- **Repo de entrega:** `2026-P4-BE/tpi-backoffice` (mono-módulo, Boot 4). **1 datasource + 2 esquemas**.
- **Mensajería:** formato oficial `EventoDTO` (5 campos + headers Kafka) **PENDIENTE** de confirmar con Usuarios/cátedra → se mantiene el envelope actual.
- **Frontend canónico:** `/FE` del Demo (`TPI---Backoffice-Demo-/FE/`).
- **US-04 → Sprint 2** (con US-07). · **US-08 acotada a T03 + T02** (+T08 REST).
- **US-03 → se agregan las tareas de rol (T10)** para cubrir sus 4 CA.
- **Scaffolding (PR #0): Luciano (Dev 1).** · **Nadie testea lo suyo** (integración por otra persona). · **Ana Paula (Dev 10):** solo MSII.
- **DoD Nivel 1:** RLS acotado a historias con datos por `course_id`.

## Notas de la división
- **Pareja por capas**, no solo por horas (columna "Capas" de la tabla).
- **US-02 T1** partida en **T1a** (migración outbox + 2 esquemas, PR #1 Día 2) + **T1b** (publisher).
- **US-08 T2a** = consumidores T03 (`challenge.events`) + T02 (`course.events`); no se consumen temas sin contrato.
- **US-02 T5** (integración Outbox) y **US-01 T8** (Testcontainers) los testean personas que **no escribieron ese código**.
- Revisiones cruzadas (US-02 T9, US-01 T10, US-08 T7, US-03 T8) sobre trabajo de **otro**.

## Referencias obligatorias
- **Auditoría:** `plan/AUDITORIA-SPRINT1.md` · **DoD:** `plan/sprint0/Sprint0-Propuesta.md` §2.
- **RULES.md / SKILLS.md:** raíz `Repositorio/TPI---Backoffice-Demo-/`.
- **Contratos:** `plan/CONTRATOS.md` · **Parámetros:** `plan/PARAMETROS.md` · **Solicitudes:** `plan/solicitudes/`.
- **Camino previo:** `plan/sprint1/tareas divididas (luciano).md` · `plan/sprint1/CAMBIOS-SPRINT1.md` · `plan/sprint1/PROPUESTA-B-CAPAS.md`.

## Registro de trabajo / trazabilidad (feedback de la IA)

Al final de **cada `dev-N.md`** se completa un **"Registro de trabajo"** con **un bloque por tarea asignada** (estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda). Práctica propia del equipo: **no** forma parte del DoD.