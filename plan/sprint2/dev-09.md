# dev-09.md (Ducart, Ana Paula) — Tareas Sprint 2 (MSII · Taiga, wiki y documentación)

> **Rol:** MSII (no codifica). Trabaja en **Taiga** (tablero y **wiki**) y en **Draw.io**. **No trabaja en los repos de GitHub:** lo que tiene que quedar en el repo (contratos `.md`, OpenAPI, `docs/`) lo escribe el dev dueño en su PR.
> **Núcleo:** 10 u · **Con gate:** 1 u · **Stretch:** 2 u · **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3 (relativos, no horas).

> **Etiquetas de tipo de trabajo** (para cargar en Taiga, una o más por tarea): Backend · Frontend · Testing · Base de Datos · DevOps · Documentación · Análisis · Diseño / UX-UI · Integración · Configuración · Seguridad · Investigación · Gestión · Otro. Criterio completo e índice maestro en `etiquetas-tareas.md`.

## Por qué cambió tu lista

En Taiga no existe ninguna página de wiki de G06, y ocho grupos ya tienen la suya (G02, G03, G05, G07, G08, G10, G11 y G12). La cátedra la pide en `guia-doc-proyecto-por-grupo`, con la plantilla `template-proyecto-por-grupo`. Ese es tu entregable principal. Las tareas que requerían PR en GitHub (C4, C7, 05-T5, 15-T6 y D3) pasaron a los devs dueños del código.

## Núcleo

| ID | Tarea | Dónde | Tamaño | CP | Etiquetas |
|---|---|---|---|---|---|
| D4 | **Taiga coherente desde el CP0** (`auditoria-sprint1.md` §5): corregir estados incoherentes (#18, #1628 y #1629 cerradas pero en "New"/"In progress"; #17, #28 y #182 en "Done" sin cerrar), pasar las 11 tareas abiertas del S1 al Sprint 2, reasignar #284 a Valentina, renombrar #3537 y #3539 (ya no calculan MAE: son fachadas de T07), cargar las historias nuevas (HT08, HU01-bis, HU10-bis, US-15) y subir el acta de la retro del S1 | Taiga | S | **CP0** | Gestión, Documentación |
| T-C | **Nueva: seguimiento de contratos.** Una tarjeta por tema fuente (T02, T03, T05, T07, T08, T10 y T11) con responsable y fecha tope en el CP2 (02/10). Se repasan en cada daily; si una vence, se avisa al responsable (él mueve su tarjeta) | Taiga | S | CP0 → CP2 | Gestión, Integración |
| W-1 | **Nueva: páginas de wiki del Sprint 1** con la plantilla de la cátedra: **"G06 - Parámetros globales y administración"** y **"G06 - Gobernanza LLM y contratos de lectura"**. Cada una lleva descripción, historias enlazadas desde el backlog, diagramas en Draw.io en el orden de la cátedra (DER → BPMN → Clases → Estados → Secuencias → Microservicios) con su explicación, y endpoints con ejemplos de request y response | Wiki + Draw.io | L | CP3 | Documentación, Diseño / UX-UI |
| D1 | Secuencia del cambio de parámetro (diferido del S1): va en la página de parámetros | Draw.io + wiki | S | CP3 | Documentación, Diseño / UX-UI |
| D5 | **Nuevo:** diagrama de microservicios "fuentes de datos → reportes" (la lámina 6 del PDF de arquitectura aplicada al Tema 12, a partir de la matriz §3.1 del plan) | Draw.io + wiki | S | CP2 | Documentación, Diseño / UX-UI |
| #316 | Página **"G06 - Reportes y panel docente"**: endpoints del panel, política RLS (explicada) y aviso `STUDENT_AT_HIGH_RISK` | Wiki | S | CP4 | Documentación, Seguridad |
| 15-T10 | En la misma página: vista del builder de US-15 y catálogo de métricas (qué mide cada una y de qué tema sale) | Wiki | S | CP5 | Documentación |
| D-DEMO | Guion de la demo del S2 + checklist E2E (panel docente, reportes dinámicos y fachada LLM); lo revisa Luciano | Wiki o Drive | S | CP5 | Documentación, Gestión |

## Con gate

| ID | Tarea | Gate | Etiquetas |
|---|---|---|---|
| 06-T6 | Secuencia ADMIN → Backoffice → T07 (calibración) en la página de gobernanza LLM | C1 | Documentación, Diseño / UX-UI |

## Stretch

| ID | Tarea | Etiquetas |
|---|---|---|
| 14-T5 | Sección de alertas configurables (catálogo de umbrales, sin evento `THRESHOLD_BREACHED`) | Documentación |
| 09-T4 | Sección de exportación asíncrona y aviso `EXPORT_READY` | Documentación |

## Qué te tienen que pasar los devs (insumos)

No tenés que leer código. Cada dueño de historia te pasa lo de su área y **revisa tu sección** antes de que la cierres:

| Insumo | Te lo pasa |
|---|---|
| Tablas y relaciones para el DER (migraciones V1–V18 y las nuevas) | Damián (read model) · Valentina (ingesta) · Mateo (registro de contratos y parámetros) |
| Ejemplos reales de request/response de parámetros y auditoría | Mateo (parámetros, PR #56) · Máximo (auditoría) |
| Ejemplos de proveedores y modelos LLM | Regina · Mateo |
| Ejemplos del panel docente y de la política RLS | Regina · Máximo · Luciano (panel FE) |
| Ejemplos de métricas, plantillas, KPIs y `run` de US-15 | Joaquín · Bruno · Damián (builder) |
| Estados del outbox (`PENDING` → `PUBLISHED` / `DEAD_LETTER`) y del riesgo (`RED`/`YELLOW`/`GREEN`) | Luciano · Damián |

## Reglas para vos (retro)

- **Taiga lo mueve cada dueño.** Vos corregís incoherencias y cargás lo nuevo; no movés tarjetas de otros sin avisarles.
- **Lo que se acuerde por WhatsApp o Discord con otro equipo no cuenta** hasta que el responsable del contrato lo vuelca al `.md` del repo. Vos lo reflejás en la tarjeta de seguimiento (T-C).
- Nombres de página con el formato de la cátedra: **"G06 - TEMA"**. Enlazá las páginas entre sí y con las historias del backlog; no dupliques contenido.

> **DoD:** páginas de wiki con la plantilla completa, diagramas con enlace editable en Draw.io y explicación, endpoints con ejemplos reales validados por su dueño, Taiga sin estados incoherentes.
