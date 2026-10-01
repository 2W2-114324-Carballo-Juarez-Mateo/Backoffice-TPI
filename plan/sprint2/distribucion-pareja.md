# Distribución pareja del Sprint 2 (v2 · 30/09/2026)

> **Regla del grupo:** el Sprint 2 es un sprint nuevo y las asignaciones se reparten **parejas en líneas de código efectivas** entre los 8 que programan, **sin arrastrar** quién tuvo más o menos líneas en el Sprint 1. Esto protege la contribución de cada uno en este sprint (por ejemplo la de Joaquín).
> **Incluye** las tareas y PRs sin terminar del Sprint 1 (en el pozo común). **Ana** (Taiga, wiki y Draw.io) no entra en el reparto de código.
> **Prevalece sobre** cualquier asignación de `tareas-sprint2.md` y de `dev-XX.md` que la contradiga (los `dev-XX.md` ya están actualizados a esta versión).

## 1 · Criterio

- **Se cuenta:** Java, SQL, TypeScript, HTML, CSS y tests. **No se cuenta:** documentación, contratos, reviews ni wiki.
- **Calibración:** PRs ya mergeadas del repo (backend: test ≈ 1,5 a 2 veces la producción; frontend: una pantalla entre 600 y 970 líneas más 200 a 800 de specs). **Margen ±30 %.**
- **Dos bolsas, cada una repartida pareja:**
  - **Núcleo (Must + Should):** 23.553 líneas → **≈ 2.944 por dev** (objetivo). Es lo que se entrega aunque no llegue ningún contrato externo.
  - **Condicionado y extra:** 8.430 líneas → **≈ 1.054 por dev**. Tareas que dependen de T07, T01 o T02 (gate C1, T01 y contratos) y las opcionales (HU09, HU14). Se reparten parejo para que, si se habilitan, nadie quede más cargado.
- **Reglas que se mantienen:** nadie testea ni revisa lo suyo · un dueño por paquete · BE + FE + tests en cada slice siempre que se pueda.

## 2 · Cuadro comparativo final

| Integrante | Núcleo (Must + Should) | Condicionado + extra | **Total (techo)** | Desvío del núcleo vs 2.944 |
|---|---:|---:|---:|---:|
| Luciano Paz | 2.925 | 1.050 | **3.975** | −0,6 % |
| Mateo Carballo | 2.990 | 1.050 | **4.040** | +1,6 % |
| Damián Baigorria | 3.020 | 1.000 | **4.020** | +2,6 % |
| Joaquín Cortez | 3.000 | 1.230 | **4.230** | +1,9 % |
| Valentina Maldonado | 2.930 | 1.150 | **4.080** | −0,5 % |
| Máximo Cerquatti | 2.898 | 990 | **3.888** | −1,6 % |
| Regina Cerasulo | 2.990 | 1.000 | **3.990** | +1,6 % |
| Bruno Gianoli | 2.800 | 960 | **3.760** | −4,9 % |
| **Total** | **23.553** | **8.430** | **31.983** | |

**Antes (versión anterior del plan):** el núcleo iba de **1.950 a 3.780** (casi el doble entre el que más y el que menos). Ahora va de **2.800 a 3.020** (diferencia de 1,08 veces).

## 3 · Detalle por integrante

| Integrante | Tarea | Tipo | Líneas |
|---|---|---|---:|
| **Luciano** | S2-00 contratos compartidos (congelado en el CP1) | Núcleo | 550 |
| | 06B-T1 orden del outbox por clave | Núcleo | 65 |
| | 05-N1 migrar a `/api/backoffice` (FE) | Núcleo | 350 |
| | 04-T4 + 05-T4 suites WireMock del cliente T07 | Núcleo | 560 |
| | #314 + #315 suite de integración de HU12 (RLS, anti-comparación, evento) | Núcleo | 600 |
| | #313 panel docente con semáforo (FE) | Núcleo | 800 |
| | 14-T3 panel de alertas (FE, HU14) | Extra | 750 |
| | #1657 estado de 2FA y sesión (FE) | Condicionado (T01) | 300 |
| **Mateo** | **PR #56: PAR-14, PAR-12 y alineación con Skill Hub (ya hecha, PR abierta)** | Núcleo (arrastre S1) | 260 |
| | 01-IT test de integración de US-01 | Núcleo (arrastre S1) | 180 |
| | 05-T1 cliente real de modelos | Núcleo | 600 |
| | #276 modal de conmutación (FE) | Núcleo | 300 |
| | 05-T3 pantalla 10 conectada (FE) | Núcleo | 450 |
| | 05-N3 specs de guards y vista de solo lectura (FE) | Núcleo | 350 |
| | #322 dashboard de KPIs con "muestra insuficiente" (FE) | Núcleo | 850 |
| | #3540 pantalla de corridas de calibración (FE) | Condicionado (C1) | 750 |
| | 08-T3 IT de consumidores T07/T05 | Condicionado (contrato) | 300 |
| **Damián** | #303 read model de cohorte (migración + entidades) | Núcleo | 420 |
| | #304 clasificador de riesgo (con R-1 y R-2) | Núcleo | 760 |
| | #312 evento `STUDENT_AT_HIGH_RISK` | Núcleo | 240 |
| | 15-T8 builder de reportes (FE) | Núcleo | 1.400 |
| | #3331 solo lectura de parámetros para PROFESSOR (FE) | Núcleo (arrastre S1) | 200 |
| | HU09 exportación asíncrona (BE) | Extra | 1.000 |
| **Joaquín** | 15-T1 catálogo de métricas | Núcleo | 550 |
| | 15-T3 plantillas (migración + CRUD) | Núcleo | 850 |
| | HU13 KPIs CSAT (migración + servicio + 2 endpoints) | Núcleo | 1.600 |
| | #3537 fachada del perfil de calibración | Condicionado (C1) | 480 |
| | #3538 pantalla del perfil de calibración (FE) | Condicionado (C1) | 750 |
| **Valentina** | #291 badge de frescura (ya escrito, falta la PR) | Núcleo (arrastre S1) | 530 |
| | #305 proyector de read models | Núcleo | 1.250 |
| | 10-M1 frescura en reportes | Núcleo | 480 |
| | 05-N2 dashboard por rol (FE) | Núcleo | 220 |
| | 05-T7 specs de pantallas 09 y 10 (FE) | Núcleo | 450 |
| | 08-T1 consumidores T07/T05 con flag | Condicionado (contrato) | 600 |
| | #284 indicador de veredicto y deriva (FE) | Condicionado (C1) | 300 |
| | 09-T2 conectar export tools al export asíncrono (FE) | Extra | 250 |
| **Máximo** | #3512 guards de rutas (ya escrito, falta la PR) | Núcleo (arrastre S1) | 98 |
| | 04-T1 cliente HTTP a T07 | Núcleo | 750 |
| | #311 acceso por rol + RLS (migración V20) | Núcleo | 1.250 |
| | 06B-T4 topic de auditoría a propiedad | Núcleo | 100 |
| | 15-T5 tests del motor de reportes | Núcleo | 700 |
| | 06B-T3 secreto del Gateway | Condicionado (T01) | 160 |
| | 06-T5 + 07-T4 tests de calibración | Condicionado (C1) | 580 |
| | 14-T4 tests de alertas | Extra | 250 |
| **Regina** | 04-T2 cliente real de proveedores | Núcleo | 660 |
| | 04-T3 pantalla 09 conectada (FE) | Núcleo | 350 |
| | #310 endpoint del panel docente | Núcleo | 700 |
| | #306 casos de riesgo (12 casos) | Núcleo | 300 |
| | 10-M2 tests de frescura y del proyector | Núcleo | 400 |
| | #323 tests de anonimato de HU13 | Núcleo | 300 |
| | 06B-T2 IT del orden del outbox | Núcleo | 280 |
| | HU14 alertas configurables (BE) | Extra | 1.000 |
| **Bruno** | 15-T2 + 15-T4 motor de reportes + `run` | Núcleo | 2.100 |
| | 15-T9 specs del builder (FE) | Núcleo | 450 |
| | 12-T9 specs del panel docente (FE) | Núcleo | 250 |
| | #3539 fachada de corridas de calibración | Condicionado (C1) | 500 |
| | 07-T2 estado de calibración del modelo activo | Condicionado (C1) | 460 |

## 4 · Qué cambió respecto de la versión anterior

| # | Cambio | Motivo |
|---|---|---|
| 1 | **PAR-14, PAR-12 y la alineación con Skill Hub (07-T1 y P-12) pasan a Mateo** (PR #56, ya hecha y abierta) | Ya está escrita por él; evita duplicar con Damián |
| 2 | **HU13 completa (backend) pasa de Mateo a Joaquín** | Nivela a Joaquín (era el más bajo) y queda cerca de su catálogo de métricas de US-15 |
| 3 | **#313 (panel docente FE) pasa de Damián a Luciano**; **15-T8 (builder FE) pasa de Luciano a Damián** | Nivela a Luciano y a Damián; el dueño del builder es quien define el riesgo que se ve en los reportes |
| 4 | **#322 (dashboard de KPIs) pasa de Valentina a Mateo** | Valentina estaba 27 % sobre el promedio |
| 5 | **#3331 pasa de Bruno a Damián**; Bruno toma **15-T9** y **12-T9** (specs) | Nivela a Bruno, que tenía el motor como única tarea grande |
| 6 | **#323 y 06B-T2 pasan a Regina** | Nivela a Regina (estaba 18 % bajo) y Máximo deja de ser "el tester del equipo" |
| 7 | **Condicionado y extra se reparte parejo:** HU14 FE y #1657 a Luciano · #3540 y 08-T3 a Mateo · HU09 FE a Valentina | Para que, si se habilitan, nadie quede sobrecargado |

## 5 · Flyway (renumerado)

**El back-merge de la release v1.0.0 (PR #58) ya se mergeó a `develop` el 30/09** (fix C1 del outbox, **`V18__update_source_contract_topics.sql`** y pom 1.0.0), así que **el V21 de Joaquín queda obsoleto**. La PR #56 de Mateo usa **V24** (sobre el `develop` ya sincronizado).

| Versión | Contenido | Dueño |
|---|---|---|
| **V18** | Ya en `develop` (topics del registro de contratos, vía PR #58) | Mateo |
| **V19** | Read model de cohorte | Damián |
| **V20** | RLS de reporting (sobre V19) | Máximo |
| **V21** | Plantillas de reportes (US-15) | Joaquín |
| **V22** | Resumen de encuestas (HU13) | Joaquín |
| **V23** | Alertas (HU14, extra) | Regina |
| **V24** | PAR-12 y alineación con Skill Hub (**PR #56, ya abierta**) | Mateo |
| **V25** | Job de exportación (HU09, extra) | Damián |
| **V26** | Checkpoint del proyector, si hace falta | Valentina |

## 6 · Revisión cruzada (nadie testea ni revisa lo suyo)

| Código de… | Lo testea | Lo revisa |
|---|---|---|
| S2-00 (Luciano) | — (sin lógica) | Máximo + Mateo |
| 06B-T1 (Luciano) | Regina (06B-T2) | Valentina (06B-T5) |
| 05-N1 y #1657 (Luciano) | Mateo (05-N3) | Joaquín (05-N4) |
| #313 (Luciano) | Bruno (12-T9) | Mateo (#317) |
| 14-T3 (Luciano) y HU14 BE (Regina) | Máximo (14-T4) | Joaquín (14-T6) |
| 04-T1 y #311 (Máximo) | Luciano (04-T4, #314) | Bruno (04-T5) · Mateo (#317) |
| 06B-T3 y 06B-T4 (Máximo) · #3512 (Máximo) | Mateo (05-N3, para #3512) | Valentina (06B-T5) · Joaquín (05-N4) |
| 04-T2 y 04-T3 (Regina) | Luciano (04-T4) · Valentina (05-T7) | Bruno (04-T5) |
| #310 (Regina) · #312 (Damián) | Luciano (#314, #315) | Mateo (#317) |
| 05-T1, #276 y 05-T3 (Mateo) | Luciano (05-T4) · Valentina (05-T7) | Joaquín (05-T6) |
| **PR #56 (Mateo)** | su propio test + Máximo (07-T4, parte PAR-14) | **Damián** (dueño original del registro) |
| 01-IT (Mateo) | — | Regina |
| #322 (Mateo) | specs propias | Damián (#325) |
| HU13 BE (Joaquín) | Regina (#323) | Damián (#325) |
| 15-T1 y 15-T3 (Joaquín) · 15-T2 y 15-T4 (Bruno) | Máximo (15-T5) | Valentina (15-T7) |
| 15-T8 (Damián) | Bruno (15-T9) | Regina (15-T11) |
| #303 y #304 (Damián) | Regina (#306) | Máximo (#308) |
| #3331 (Damián) | Mateo (05-N3) | Joaquín (05-N4) |
| #305 y 10-M1 (Valentina) | Regina (10-M2) | Máximo (#308) |
| #291 y 05-N2 (Valentina) | Mateo (05-N3) | Joaquín (05-N4) |
| HU09: BE (Damián) y FE (Valentina) | Máximo | Luciano |
| 08-T1 (Valentina) | Mateo (08-T3) | Luciano (08-T5) |
| #3537 y #3538 (Joaquín) · #3539 y 07-T2 (Bruno) · #3540 (Mateo) · #284 (Valentina) | Máximo (06-T5, 07-T4) | Regina (06-T7) · Damián (07-T6) |

## 7 · Riesgos de esta distribución

- **Joaquín** queda con el núcleo más pesado en backend (HU13 + catálogo + plantillas). Se mitiga porque HU13 y US-15 comparten el modelo de "solo agregados con PAR-18 + curso cerrado". Si en el CP2 su avance no acompaña, `#323` y 15-T3 son las primeras en moverse a quien tenga margen.
- **Bruno** queda con el motor (XL) y dos suites de specs del FE. Si el motor se demora, las specs (15-T9 y 12-T9) se pueden entregar al final.
- **Damián** hace el builder (1.400 líneas de FE) y depende del catálogo de Joaquín y del motor de Bruno para probarlo con datos reales. Puede avanzar contra el contrato congelado de S2-00.
- **Regina** hace las suites de outbox y de anonimato: necesita Testcontainers con Kafka y PostgreSQL. Máximo la acompaña en 06B-T2 (hizo el test de publicación en el Sprint 1).
- Las cifras son estimaciones con un margen de ±30 %. Si al terminar el CP2 las diferencias reales superan el 20 %, se rebalancea con las tareas de menor dependencia.
