# dev-05.md (Maldonado, Valentina) — Tareas Sprint 2

> **Núcleo:** 2.930 líneas · **Condicionado y extra:** 1.150 · **Total techo:** 4.080 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

## Núcleo

| ID | Tarea | Capa | Líneas | CP |
|---|---|---|---:|---|
| #291 | **Badge de frescura: ya está re-aplicado con los fixes de la review** en `feature/mvp-s7-ingestion-ui` (`d175ca1`). Sincronizar con `develop` y abrir la PR; dejar el componente reutilizable para los reportes | FRONT | 530 | **CP0** |
| #305 | Proyector desde `reporting.ingested_event` hacia el read model de V19 (T03 `CHALLENGE_COMPLETED`, T02 `ROSTER_UPDATED`; T05/T10 detrás de flag): idempotente, con checkpoint (**V26** si hace falta), cada ≤ 5 min (siempre menor que PAR-23). **Revisar antes el mapeo con el contrato oficial de T03:** Skill Hub trae `result.status` (`APPROVED`/`DISAPPROVE`) e `id_user`, pero `ChallengeCompletedPayload` usa `resultado` y `studentId` | BACK | 1.250 | CP3 |
| 10-M1 | `DataFreshnessProvider` → `DataFreshnessDto {asOf, stale, thresholdMinutes, sources[]}` calculado **al leer** con `IngestionStatsQuery` y PAR-23. No es un `@Scheduled` | BACK | 480 | CP3 |
| 05-N2 | Dashboard: ocultar accesos no permitidos a GESTOR y PROFESSOR | FRONT | 220 | CP3 |
| 05-T7 | Specs de las pantallas 09 y 10 conectadas | TEST | 450 | CP4 |
| | **Subtotal núcleo** | | **2.930** | |

## Condicionado y extra

| ID | Tarea | Capa | Líneas | Condición |
|---|---|---|---:|---|
| 08-T1 | Consumidores de `llm.events` (T07, filtrando por `eventType`) y de T05, con flag, dedup y DLT (mismo patrón que `RawEnvelopeIngestor`) | BACK | 600 | Gate: contrato T07/T05 |
| #284 | Indicador de veredicto y deriva, y banner de conmutación (**en Taiga figura Joaquín: pedirle a Ana que lo reasigne**) | FRONT | 300 | Gate: C1 |
| 09-T2 | Conectar las export tools de la slice 07 al export asíncrono de HU09 | FRONT | 250 | Extra |
| | **Subtotal** | | **1.150** | |

## Sin líneas de código (documentación y revisión)

- **C8:** confirmar con T03 los valores posibles de `resultado` (o `result.status`) y completar la fila de T03 en la tabla de firmas. **08-T4:** mapeo de contratos de lectura de T07 y T05.
- **Revisás:** 06B-T5 (concurrencia del outbox y secreto del Gateway) · 15-T7 (seguridad del motor de US-15).
- **Sale de tu lista:** #322 (dashboard de KPIs) pasó a Mateo.
- **Insumos para la wiki (Ana):** tablas de ingesta y del read model para el DER.

## Archivos

- **Tuyos:** `reporting/services/projection/*`, `DataFreshnessProvider` (impl), `V26`, consumidores nuevos de T07/T05, FE badge de frescura, FE dashboard por rol, specs de las pantallas 09 y 10.
- **No los tocás:** el esquema de V19 (Damián): si el proyector necesita una columna, se la pedís antes del CP2.

## Dependencias

- **Dependés de:** V19 (Damián, CP2) para mergear #305 (podés desarrollar antes contra el esquema acordado) · S2-00.
- **Dependen de vos:** todo endpoint de reporte usa tu `DataFreshnessProvider`.

## Te testean / revisan

#305 y 10-M1 → Regina (10-M2), revisa Máximo (#308) · #291 y 05-N2 → specs Mateo (05-N3), revisa Joaquín (05-N4) · 08-T1 → Mateo (08-T3), revisa Luciano (08-T5) · HU09 FE → tests Máximo, revisa Luciano.

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · OpenAPI y `docs/` al día en la misma PR.
