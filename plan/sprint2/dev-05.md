# dev-05.md (Maldonado, Valentina) — Tareas Sprint 2

> **Disponibilidad:** media · **Núcleo:** 14 u · **Con gate:** 4 u · **Stretch:** — · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| #291 | **No hay que rehacerlo:** el badge ya está re-aplicado con los fixes de la review en `feature/mvp-s7-ingestion-ui` (`d175ca1`). Abrir la PR a `develop` (sincronizar antes con `develop`) y dejar el componente reutilizable para la cabecera de los reportes | FRONT | S | **CP0** |
| C8 | **Nueva: dueña del contrato con T03.** Confirmar los valores posibles de `resultado` en `CHALLENGE_COMPLETED` (el proyector los necesita para contar aprobados y reprobados), volcarlo a `CONTRATOS_T03_RESPUESTA.md` y completar la fila de **T03** en la tabla de firmas de `CONTRATOS.md` | DOC | S | CP0 → CP2 |
| #305 | Proyector desde `reporting.ingested_event` hacia el read model de V18 (T03 `CHALLENGE_COMPLETED`, T02 `ROSTER_UPDATED`; T05/T10 detrás de flag): idempotente, con checkpoint (`V26` si hace falta), cada ≤ 5 min con propiedad tipada (siempre < PAR-23) | BACK | L | CP3 |
| 10-M1 | **Redefinida:** `DataFreshnessProvider` → `DataFreshnessDto {asOf, stale, thresholdMinutes, sources[]}` calculado **al leer** con `IngestionStatsQuery` y PAR-23, según las fuentes que declara cada reporte. **No** es un `@Scheduled` que marca `isStale` (en `develop` la frescura ya se calcula al leer) | BACK | M | CP3 |
| 05-N2 | Dashboard: ocultar accesos no permitidos a GESTOR y PROFESSOR | FRONT | S | CP3 |
| #322 | Dashboard de KPIs con "muestra insuficiente" y "disponible al cierre del curso" contra `CsatKpiDto` | FRONT | M | CP4 |
| 05-T7 | Specs de las pantallas 09 y 10 conectadas | TEST | M | CP4 |
| 06B-T5 | Peer review de concurrencia del outbox y del secreto del Gateway | REV | S | CP1–CP4 |
| 15-T7 | Peer review de seguridad del motor de US-15 (RLS, lista blanca) | REV | S | CP4 |

## Con gate

| ID | Tarea | Capa | Tamaño | Gate |
|---|---|---|---|---|
| 08-T1 | Consumidores de `llm.events` (T07, filtrando por `eventType`) y de T05, con flag, dedup y DLT (mismo patrón que `RawEnvelopeIngestor`) | BACK | M | Contrato T07/T05 |
| 08-T4 | Actualizar el mapeo de contratos de lectura con T07 y T05 | DOC | S | Ídem |
| #284 | Indicador de veredicto/deriva y banner de conmutación automática (**en Taiga figura Joaquín: pedirle a Ana que lo reasigne**) | FRONT | S | C1 |

## Revisiones que te tocan

- **V21** (Joaquín): corrección de topics del registro de contratos.

## Insumos para la wiki (Ana)

Pasale las tablas de ingesta y del read model para el DER, y revisá su sección antes de que la cierre.

## Fuera de tu lista (vs propuesta)

- **B-AL** (alerta de presupuesto LLM) pasa al backlog: `llm.budget.events` no está registrado en T11 y el contrato con T07 no está firmado.

## Archivos

- **Tuyos:** `reporting/services/projection/*`, `DataFreshnessProvider` (impl), `V26`, consumidores nuevos de T07/T05, FE badge de frescura y dashboard de KPIs.
- **No los tocás:** el esquema de `V18` (Damián): si el proyector necesita una columna, se la pedís a Damián antes del CP2. Los KPIs (Mateo) los consumís por DTO.

## Dependencias

- **Dependés de:** V18 (Damián, CP2) para mergear #305 (podés desarrollar antes contra el esquema acordado) · S2-00.
- **Dependen de vos:** todo endpoint de reporte usa tu `DataFreshnessProvider`.

## Te testean / revisan

#305 · 10-M1 → Regina (10-M2), revisa Máximo (#308) · #291/05-N2 → Mateo (05-N3), revisa Joaquín (05-N4) · #322 → revisa Damián (#325) · 08-T1 → Bruno (08-T3), revisa Luciano (08-T5).

## Si en el CP2 no llegó el contrato de T07/T05

08-T1/08-T4 quedan con el flag apagado y pasan a "Necesita información"; #284 sigue el gate C1.

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** CA de HU11 (proyección) y HU10-bis, docs al día.
