# Tareas Sprint 1 — Índice (división 9 devs)

> Backlog del **Sprint 1** (US-01, US-02, US-03, US-04, US-08 + Infra) ya dividido entre **9 desarrolladores**. Balanceado por **horas totales** y por **horas de código (BACKEND+TEST)**.

## Archivos
| Archivo | Contenido |
|---|---|
| **[tareas-sprint1.md](tareas-sprint1.md)** | Todas las tareas del sprint, agrupadas por US y asignadas a cada dev |
| **[dev-1.md](dev-1.md)** | Tareas de Dev 1 + "Qué hacer" + CA + DoD |
| **[dev-2.md](dev-2.md)** | Tareas de Dev 2 |
| **[dev-3.md](dev-3.md)** | Tareas de Dev 3 |
| **[dev-4.md](dev-4.md)** | Tareas de Dev 4 |
| **[dev-5.md](dev-5.md)** | Tareas de Dev 5 |
| **[dev-6.md](dev-6.md)** | Tareas de Dev 6 |
| **[dev-7.md](dev-7.md)** | Tareas de Dev 7 |
| **[dev-8.md](dev-8.md)** | Tareas de Dev 8 |
| **[dev-9.md](dev-9.md)** | Tareas de Dev 9 |

## Resumen de carga

| Dev | Tareas | Total | Código (BE+TEST) |
|---|---|---:|---:|
| **Dev 1** | US-02 T1a + T1b + T2 + T8 · Infra | 22 | 18 |
| **Dev 2** | US-02 T3 + T4 + T5 + T9 | 21 | 18 |
| **Dev 3** | US-01 T1 + T2 + T7 + T10 | 21 | 18 |
| **Dev 4** | US-01 T3 + T4 + T8 + T9 | 22 | 18 |
| **Dev 5** | US-02 T6 + T7 · US-08 T1 + T4 · US-03 T8 | 21 | 18 |
| **Dev 6** | US-08 T2a + T2b + T3 + T6 | 22 | 16 |
| **Dev 7** | US-08 T5 + T7 · US-03 T1 + T5 | 21 | 18 |
| **Dev 8** | US-03 T2 + T7 · US-04 T1 + T3 | 20 | 16 |
| **Dev 9** | US-04 T2 + T5 + T6 + T7 + T8 | 22 | 16 |

**Total:** 192h · rango 20–22h totales · 16–18h de código.

## Notas de la división
- Se pasó de **10 a 9 devs** (el Dev 10 no codifica).
- **US-02 T1** partida en **T1a** (migración `outbox_events`, merge Día 1) + **T1b** (publisher).
- **US-08 T2** partida en **T2a** (T02/T04/T05) + **T2b** (T06/T07/T08/T10).
- Se eliminó la dependencia **US-04 → outbox** (US-04 no publica eventos).
- Revisiones al final (no bloquean arranques).

## Referencias obligatorias
- **DoD (Nivel 0/1/2):** `plan/sprint0/Sprint0-Propuesta.md` §2.
- **RULES.md / SKILLS.md:** raíz del repo de trabajo `Repositorio/TPI---Backoffice-Demo-/`.
- **Contratos:** `plan/CONTRATOS.md` · **Parámetros:** `plan/PARAMETROS.md`.
- **Backlog general:** `plan/tareas.md` · **Original (10 devs):** `plan/sprint1/tareas divididas (luciano).md`.