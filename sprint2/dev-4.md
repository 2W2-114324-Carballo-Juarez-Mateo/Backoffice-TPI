# dev-4.md (Cortez, Joaquín) — Tareas del Sprint 2

> **Capacidad:** 27,7 h · **Asignado:** 20 h (72 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> **Opción A:** tus tareas del golden set se redefinen. Los casos y el golden set son de T07; el Backoffice es solo la fachada del ADMIN.
> Incluye tareas que eran de Julieta: 05-T6 y #324.

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 2–5 | #3537 | BACK | **Redefinida:** fachada del perfil de calibración institucional (golden set y rúbrica) sobre T07 (`GET/POST /admin/institutional-calibration/profile`). Rama `feature/tema-12-calibration-facade` | 4 | 04-T1 (Máximo), C1 |
| 5 | 06-T8 | DOC | OpenAPI de la fachada de calibración | 1 | #3537, #3539 |
| 3–6 | #3538 | FRONT | **Redefinida:** pantalla del perfil de calibración (parte 12), reemplazando el placeholder | 5 | contrato de #3537 |
| 5 | 05-T6 | REV | Peer review de la fachada de modelos (05-T1, #276 y 05-T3): mapeo de errores, unicidad delegada en T07 | 1 | 05-T1 |
| 6–7 | #284 | FRONT | Indicador de veredicto (PASSED/FAILED) y deriva, y banner de conmutación automática | 3 | 07-T2 (Bruno) |
| 7 | 05-N4 | REV | Peer review de guards (#3512), migración de rutas (05-N1), badge (#291), dashboard (05-N2) y 2FA (#1657) | 2 | HT05 |
| 7–8 | #324 | DOC | Políticas de privacidad y fórmulas de agregación | 2 | #319, #320 |
| 9 | 12-T9 | TEST | Specs del panel docente (#313) | 2 | #313 |

**Revisan tu trabajo:** Máximo testea la fachada de calibración (06-T5) y el indicador (07-T4); Regina revisa la fachada (06-T7) y Mateo revisa HU07 (07-T6).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] UI Kit `@2026-p4-fe/ui` (`Generic*`), textos de UI en español, código en inglés
- [ ] PR revisada por otra persona · sin secretos · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
