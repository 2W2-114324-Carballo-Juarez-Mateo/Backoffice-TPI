# dev-6.md (Maldonado, Valentina) — Tareas del Sprint 2

> **Capacidad:** 35,3 h · **Asignado:** 27 h (76 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> Incluye una tarea que era de Julieta: 05-N2.

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 1–2 | 05-N2 | FRONT | Dashboard: ocultar los accesos no permitidos a GESTOR y PROFESSOR | 2 | #3512 |
| 1–3 | #291 | FRONT | Rehacer el badge de frescura de la cabecera: se mergeó en la PR #91 y se revirtió en la #99, hay que revisar el motivo del revert antes de rehacerlo | 3 | — |
| 3–5 | #305 | BACK | Job programado de recálculo del read model desde `ingested_event` (T03 `challenge.events` y T02 `course.events`) | 5 | #303 (V18), #304 |
| 4 | 06B-T5 | REV | Peer review de concurrencia del outbox (06B-T1) y del secreto del Gateway (06B-T3/T4) | 2 | 06B-T1 |
| 4–6 | 08-T1 | BACK | Consumidores de `llm.events` (T07, filtro por `eventType` según `llm-service-kafka-contract`) y de T05, detrás de flags, con deduplicación y DLT. Rama `feature/tema-12-hu08-consumers` | 5 | contratos de T07 y T05 |
| 6 | 08-T4 | DOC | Actualizar el mapeo de contratos de lectura con T07 y T05 | 2 | 08-T1 |
| 5–6 | 05-T7 | TEST | Specs de las pantallas 09 y 10 conectadas | 3 | 04-T3, 05-T3 |
| 7–9 | #322 | FRONT | Dashboard de KPIs con aviso de "muestra insuficiente" | 5 | contrato de #321 |

**Revisan tu trabajo:** Bruno testea 08-T1 (08-T3) y Luciano lo revisa (08-T5); Regina testea #305 (#306); Mateo testea #291 y 05-N2 (05-N3) y Joaquín los revisa (05-N4); Máximo testea #322 (#323).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] Deduplicación por `eventId` y DLT verificados con Testcontainers
- [ ] PR revisada por otra persona · sin secretos · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
