# dev-10.md (Ducart, Ana Paula) — Tareas del Sprint 2 · solo MSII

> **Capacidad:** 25,2 h · **Asignado:** 19 h (75 %) · **Capas:** DOC (diagramas, contratos, planificación; sin código)
> **Rama de docs:** `feature/tema-12-docs-sprint2`, con PR a `develop`.

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 1–2 | D4 | DOC | Carga y sincronización de Taiga del Sprint 2, y acta de la retro del Sprint 1 | 2 | planning |
| 1–3 | C4 | DOC | Corregir el §6 del contrato de T07 (fachada: el Backoffice opera `/admin/*` de T07, sin tablas LLM propias) y registrar los acuerdos de C1, C2 y C3 en `CONTRATOS.md` (fecha, responsables de cada lado, versión y evidencia) | 3 | C1–C3 |
| 2–3 | 05-T5 | DOC | Contrato: `MODEL_CHANGED` lo publica T07 y el Backoffice deja de emitir `ModelProviderChanged` | 2 | — |
| 3–4 | D1 | DOC | Diagrama de secuencia del cambio de parámetro: ADMIN → Gateway → Backoffice → Outbox → Kafka (quedó diferido del Sprint 1) | 3 | — |
| 4–6 | 06-T6 | DOC | Diagrama de secuencia ADMIN → Backoffice → T07 (perfil de calibración, corridas y veredicto) | 3 | C1 |
| 7–8 | #316 | DOC | Endpoints del panel docente, política RLS (`app.current_course`, `ALL` solo ADMIN) y contrato de la alerta `StudentAtHighRisk` | 3 | #310–#312 |
| 8–10 | D3 | DOC | Sincronizar `docs/backend/docs` y el sitio con lo real: fachada T07, auditoría vía T01, rutas `/backoffice`, roles del Gateway v3 (`ADMIN`, `GESTOR`, `PROFESSOR`, `STUDENT`, `MS`) | 3 | todo el sprint |

> **Coordinación:** los cambios en documentación compartida y en `AGENTS.md` se avisan en el canal antes de mergear. `AGENTS.md` todavía dice `PROFESOR`/`ALUMNO`: proponé el cambio, no lo apliques sin acuerdo del equipo.

## Checklist de DoD (documentación)

- [ ] Diagramas versionados en el sitio y referenciados desde el SDD
- [ ] Contratos con estado, fecha, responsables y evidencia
- [ ] PR revisada por otra persona
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, pendientes -->
