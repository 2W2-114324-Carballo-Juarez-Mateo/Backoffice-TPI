# dev-9.md (Gianoli, Bruno) — Tareas del Sprint 2 ★ foco del PO

> **Capacidad:** 28,4 h · **Asignado:** 24 h (85 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> **Opción A:** la PR #30 (MAE propio) quedó reemplazada. T07 calcula `maeFinal` y el veredicto; vos hacés la fachada de corridas y la pantalla 13. Las tareas cerradas #281–#285 quedan como "reemplazadas".

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 2–5 | #3539 | BACK | **Redefinida:** fachada de corridas de calibración: crear, listar y detalle (`GET/POST /admin/institutional-calibration/runs`, `GET …/runs/{runId}`), con `maeFinal`, `maxIndividualError` y el veredicto de T07. Rama `feature/tema-12-calibration-facade` | 5 | 04-T1 (Máximo), C1 |
| 3 | 04-T5 | REV | Peer review de seguridad de credenciales y del cliente T07 (04-T1 y 04-T2) | 2 | 04-T1, 04-T2 |
| 4–6 | 07-T2 | BACK | Endpoint del estado de calibración del modelo activo: último veredicto y marca de deriva, leídos de T07 | 4 | #3539 |
| 3–6 | #3540 | FRONT | **Redefinida:** pantalla de corridas de calibración (parte 13): lista, detalle y errores por dimensión | 5 | contrato de #3539 |
| 5 | #286 | DOC | **Redefinida:** documentar que T07 calcula el MAE y el veredicto, que el Backoffice gobierna PAR-14 y que la deriva la emite T07 | 2 | — |
| 7 | 08-T3 | TEST | Tests de integración de los consumidores de T07 y T05: nuevo, duplicado, malformado → DLT, flag apagado | 3 | 08-T1 (Valentina) |
| 7–8 | #3331 | FRONT | Solo lectura de parámetros para PROFESSOR: depende del permiso de lectura de T01 en `core/` (hoy `/admin` exige `manageUsers` y `parameters` exige `editGlobalConfig`) | 3 | T01 |

**Revisan tu trabajo:** Máximo testea #3539 y 07-T2 (06-T5, 07-T4); Regina revisa la fachada (06-T7) y Mateo revisa HU07 (07-T6); Mateo testea #3331 (05-N3).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] 200/403 por rol · errores de T07 mapeados a `ErrorApi` (nunca un 500 por un 401/403 de T07)
- [ ] PR revisada por otra persona · sin secretos · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
