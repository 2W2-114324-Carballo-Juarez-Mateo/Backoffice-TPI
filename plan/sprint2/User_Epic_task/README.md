# User_Epic_task — Épicas, historias y tareas del Sprint 2 (G06)

> Sprint 2: 28/09 → 11/10/2026 · Backoffice Tema 12 · Plantillas de la cátedra (épica e historia de usuario) y nombre de tarea `[G06] - [TIPO] - descripción`.

## Contenido

| Carpeta / archivo | Qué tiene |
|---|---|
| `epicas/` | Las 5 épicas de G06 con la plantilla de épica |
| `historias/` | Las 20 historias del sprint con la plantilla de historia de usuario y sus tareas asociadas |
| `tareas-por-persona.md` | Las tarjetas de cada integrante, ordenadas por checkpoint |

## Números

- **20 historias:** 18 ya existen en Taiga y se mueven al Sprint 2, y 2 se crean (HU15 y HU16).
- **117 tareas:** 45 ya existen en Taiga (se mueven, se renombran o se reasignan) y 72 se crean.

## Historias por épica

| Épica | Historias |
|---|---|
| #2 Parámetros Globales | [HU01](historias/HU01_parametros-globales.md), [HU02](historias/HU02_propagacion-cambio-parametro.md), [HT02](historias/HT02_infra-release-gateway.md), [HT03](historias/HT03_documentacion-wiki.md), [HT04](historias/HT04_frontend-conexion-backend.md) |
| #4 Administración de la Plataforma | [HU03](historias/HU03_rol-administrador.md) |
| #3 Configuración de Modelos LLM y Golden Set | [HU04](historias/HU04_proveedores-modelos-ia.md), [HU05](historias/HU05_conmutacion-modelos-ia.md), [HU06](historias/HU06_golden-set-calibracion.md), [HU07](historias/HU07_tolerancia-par14-deriva.md) |
| #5 Observabilidad, Reportes y Panel de Riesgo | [HU11](historias/HU11_read-model-riesgo.md), [HU12](historias/HU12_panel-docente.md), [HU13](historias/HU13_indicadores-csat.md), [HU14](historias/HU14_umbrales-alertas.md), [HU09](historias/HU09_exportacion-reportes.md), [HU15](historias/HU15_reportes-dinamicos-motor.md), [HU16](historias/HU16_constructor-reportes.md) |
| #6 Contratos de Lectura e Ingesta | [HU08](historias/HU08_ingesta-temas.md), [HU10](historias/HU10_frescura-datos.md), [HT01](historias/HT01_contratos-entre-temas.md) |

## Prioridad

| Prioridad | Historias |
|---|---|
| Must | HU01 (5), HU02 (5), HU03 (5), HU04 (5), HU05 (5), HU10 (3), HU11 (5), HU12 (5), HT01 (5), HT02 (3), HT03 (3), HT04 (5), HU15 (5) |
| Should | HU06 (5), HU07 (5), HU08 (5), HU13 (5), HU16 (3) |
| Could | HU14 (3), HU09 (5) |

## Cómo leerlo

1. **Nombre de tarea:** `[G06] - [TIPO] - descripción`. TIPO es uno de los cinco que ya usa Taiga: BACKEND, FRONTEND, TEST, DOCUMENTACION, REVISION.
2. **Tipos nuevos que se absorbieron:** las tareas de repositorio y ramas (DevOps) se nombran como BACKEND y las de gestión de Taiga como DOCUMENTACION, para no inventar un tipo que el tablero no tiene. La **etiqueta** (Tags) sí conserva el tipo de trabajo real (DevOps, Gestión).
3. **Columna Taiga:** `#NNN` es una tarjeta que ya existe; "Nueva" se crea. En la tarjeta existente se cambia el nombre si difiere del de esta carpeta.
4. **Checkpoint (CP):** CP0 29–30/09 · CP1 01/10 · CP2 02/10 · CP3 06/10 · CP4 08/10 · CP5 09–11/10.
5. **Responsable:** es el dueño de la tarea según `distribucion-pareja.md`. Nadie revisa ni testea su propio código.

## Pendientes de decisión

- **HU09 (#20) está hoy en "G01 - Sprint 1"**, un sprint de otro grupo: hay que sacarla de ahí antes de moverla al Sprint 2.
- **Nombres de PAR-14:** `average` vs `promedio` depende de la confirmación de T07.
- **Taiga no se modificó:** esta carpeta es el insumo para que cada dueño cargue o mueva sus tarjetas.
