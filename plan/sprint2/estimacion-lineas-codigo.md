# Estimación de líneas de código efectivas — Sprint 2 (v2, distribución pareja)

> **Fecha:** 30/09/2026 · **Reparto definitivo:** `distribucion-pareja.md` · **Detalle por integrante:** `dev-01.md` a `dev-08.md`
> **Qué cuenta:** Java, SQL, TypeScript, HTML, CSS y tests. **No cuenta:** documentación, contratos, reviews ni wiki (por eso Ana no figura).
> **Margen de error:** ±30 %. Es una estimación para comparar cargas, no una promesa.

## 1 · Cómo se estimó

Se calibró con PRs ya mergeadas del repo:

| Referencia | Producción | Tests |
|---|---:|---:|
| BE PR #36 (modelos LLM) | 547 | 799 |
| BE PR #31 (ingesta) | 925 | 1.353 |
| BE PR #33 (proveedores) | 749 | 846 |
| BE PR #45 (contratos de lectura) | 407 | 786 |
| FE PR #78 (pantalla de proveedores) | 967 | 318 |
| FE PR #79 (pantalla de modelos) | 602 | 199 |

- **Backend:** el test pesa entre 1,5 y 2 veces la producción (Checkstyle, PMD y JaCoCo ≥ 90 %).
- **Frontend:** una pantalla tiene entre 600 y 970 líneas de código y entre 200 y 800 de specs.

## 2 · Reparto definitivo (pareja, sin arrastrar el Sprint 1)

| Integrante | Núcleo (Must + Should) | Condicionado + extra | **Total (techo)** |
|---|---:|---:|---:|
| Luciano Paz | 2.925 | 1.050 | **3.975** |
| Mateo Carballo | 2.990 | 1.050 | **4.040** |
| Damián Baigorria | 3.020 | 1.000 | **4.020** |
| Joaquín Cortez | 3.000 | 1.230 | **4.230** |
| Valentina Maldonado | 2.930 | 1.150 | **4.080** |
| Máximo Cerquatti | 2.898 | 990 | **3.888** |
| Regina Cerasulo | 2.990 | 1.000 | **3.990** |
| Bruno Gianoli | 2.800 | 960 | **3.760** |
| **Total** | **23.553** | **8.430** | **31.983** |

- **Núcleo:** lo que se entrega aunque no llegue ningún contrato externo. Objetivo: 2.944 por persona; el desvío máximo es de −4,9 % (Bruno) y +2,6 % (Damián).
- **Condicionado y extra:** depende de T07, T01 o T02, o es opcional (HU09, HU14). Objetivo: 1.054 por persona.
- **Incluye lo pendiente del Sprint 1** dentro del núcleo: #291 (530 líneas, Valentina), #3512 (98, Máximo), PR #56 (260, Mateo), 01-IT (180, Mateo) y #3331 (200, Damián).

## 3 · Antes y después

| Integrante | Núcleo antes | Núcleo ahora | Cambio |
|---|---:|---:|---:|
| Luciano | 3.525 | 2.925 | −600 |
| Mateo | 3.740 | 2.990 | −750 |
| Damián | 2.670 | 3.020 | +350 |
| Joaquín | 1.950 | 3.000 | **+1.050** |
| Valentina | 3.780 | 2.930 | −850 |
| Máximo | 3.178 | 2.898 | −280 |
| Regina | 2.410 | 2.990 | +580 |
| Bruno | 2.300 | 2.800 | +500 |
| **Diferencia entre el que más y el que menos** | **1,94 veces** | **1,08 veces** | |

(El "antes" incluye lo pendiente del Sprint 1 y la PR #56 asignada a Mateo, para que la comparación sea justa.)

## 4 · Reasignaciones que produjeron el cambio

| Tarea | De | A | Líneas |
|---|---|---|---:|
| PR #56 (PAR-14, PAR-12, Skill Hub; ya hecha) | Damián | **Mateo** | 260 |
| HU13: KPIs CSAT, backend completo | Mateo | **Joaquín** | 1.600 |
| #313 panel docente (FE) | Damián | **Luciano** | 800 |
| 15-T8 builder de reportes (FE) | Luciano | **Damián** | 1.400 |
| #322 dashboard de KPIs (FE) | Valentina | **Mateo** | 850 |
| #3331 solo lectura PROFESSOR (FE) | Bruno | **Damián** | 200 |
| 15-T9 specs del builder | Damián | **Bruno** | 450 |
| 12-T9 specs del panel | Joaquín | **Bruno** | 250 |
| #323 tests de anonimato | Joaquín | **Regina** | 300 |
| 06B-T2 IT del orden del outbox | Máximo | **Regina** | 280 |
| Condicionado/extra: HU14 FE y #1657 | Regina | **Luciano** | 1.050 |
| Condicionado/extra: #3540 y 08-T3 | Bruno | **Mateo** | 1.050 |
| Extra: HU09 FE | Damián | **Valentina** | 250 |

## 5 · Flyway y choques que se resolvieron

| Problema | Solución |
|---|---|
| **Tarea duplicada:** 07-T1 y P-12 ya estaban hechas por Mateo (PR #56) y asignadas también a Damián | Quedan **solo de Mateo**; Damián las revisa |
| **V18 ocupada:** la release v1.0.0 (PR #58) ya trae `V18__update_source_contract_topics.sql` | El read model de Damián pasa a **V19**; RLS a **V20**; plantillas a **V21**; resumen de encuestas a **V22** (Joaquín); alertas **V23**; export **V25**; checkpoint **V26**. Se mergea primero la PR #58 |
| **V21 de Joaquín obsoleto** (corregir topics del registro) | Ya lo hace la V18 de la release |
| **V24 ocupada** por la PR #56 | Se respeta: es de Mateo |

## 6 · Riesgos de la distribución

- **Joaquín** queda con el backend más pesado (catálogo, plantillas y HU13). Si en el CP2 no acompaña, #323 y 15-T3 son las primeras en moverse.
- **Bruno** queda con el motor (XL) más dos suites de specs. Si el motor se demora, las specs se entregan al final.
- **Damián** hace el builder (1.400 líneas de FE) y depende del catálogo y del motor para probarlo; puede avanzar contra el contrato congelado.
- **Regina** hace las suites de outbox y de anonimato: necesita Testcontainers con Kafka y PostgreSQL. Máximo la acompaña en 06B-T2.
- Si al terminar el CP2 la diferencia real entre integrantes supera el 20 %, se rebalancea con las tareas de menor dependencia.

## 7 · Puntos abiertos que siguen del plan de Mateo

- **Renombrar `promedio` por `average`** en PAR-14: hay que confirmarlo con T07 antes de mergear la PR #56, porque cambia un valor ya cargado.
- **Payload de T03:** Skill Hub trae `result.status` (`APPROVED`/`DISAPPROVE`) e `id_user`, pero `ChallengeCompletedPayload` usa `resultado` y `studentId`. Valentina lo revisa antes del proyector (#305).
- **`usoTutorIa` no existe en T03:** la métrica de tutor IA queda fuera del catálogo de US-15.
- Las citas de Skill Hub no las pude verificar: vienen del plan de Mateo.
