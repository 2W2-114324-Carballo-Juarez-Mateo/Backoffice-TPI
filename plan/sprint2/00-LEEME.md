# Sprint 2 — Paquete de planificación (TPI-G06 · Backoffice)

> Plan canónico del Sprint 2 (28/09 → 11/10/2026). Armado a partir de la propuesta del grupo, la auditoría del Sprint 1 (ramas, Taiga, PRD y contratos) y la retrospectiva del 28/09. **Última actualización: 30/09/2026 — distribución pareja en líneas de código.**

## Qué hay en esta carpeta

| Archivo | Para qué | Quién lo lee |
|---|---|---|
| **`distribucion-pareja.md`** | **Reparto por integrante, parejo en líneas de código efectivas** (incluye lo pendiente del Sprint 1), Flyway renumerado y revisión cruzada. **Prevalece sobre lo que contradiga `tareas-sprint2.md`** | Todos |
| **`tareas-sprint2.md`** | Plan canónico: decisiones, alcance, contratos de lectura, slices, checkpoints, corte, riesgos | Todos |
| `dev-01.md` … `dev-09.md` | Tareas de cada integrante, con líneas, archivos propios, dependencias y quién los revisa | Cada dev |
| `etiquetas-tareas.md` | Etiqueta de tipo de trabajo de cada tarea (Backend, Frontend, Testing, etc.), criterio y conteo por integrante. Cada dueño las carga en Taiga | Todos, Ana |
| `estimacion-lineas-codigo.md` | Cómo se estimaron las líneas, antes y después del reparto | Quien coordina |
| `revision-pr.md` | Checklists de revisión por PR y comentarios para las ramas pendientes | Autores y revisores |
| `auditoria-sprint1.md` | Qué quedó del Sprint 1: estado por área, PRD, ramas fuera de `develop`, Taiga, PRs | Quien coordina, Ana |
| `correcciones-propuesta.md` | Cada cambio hecho sobre la propuesta original, con la evidencia | Todos |
| `retrospectiva-sprint1.md` | Acta de la retro, datos y cómo cada acuerdo queda en el plan | Todos, Ana |

| Dev | Integrante | Núcleo | Con condicionado y extra |
|---|---|---:|---:|
| dev-01 | Paz, Luciano | 2.925 | 3.975 |
| dev-02 | Carballo Juarez, Mateo | 2.990 | 4.040 |
| dev-03 | Baigorria, Damián | 3.020 | 4.020 |
| dev-04 | Cortez, Joaquín | 3.000 | 4.230 |
| dev-05 | Maldonado, Valentina | 2.930 | 4.080 |
| dev-06 | Cerquatti, Máximo | 2.898 | 3.888 |
| dev-07 | Cerasulo, Regina | 2.990 | 3.990 |
| dev-08 | Gianoli, Bruno | 2.800 | 3.760 |
| dev-09 | Ducart, Ana Paula (MSII: Taiga, wiki y Draw.io; no trabaja en los repos) | — | — |

> **Numeración corrida desde el Sprint 2.** En el Sprint 1 el 05 era Julieta, que ya no está en el equipo. Equivalencias con el S1 y con la propuesta original: Valentina 06 → 05 · Máximo 07 → 06 · Regina 08 → 07 · Bruno 09 → 08 · Ana 10 → 09.

## Antes de arrancar (CP0)

1. **Mateo:** mergear primero el back-merge de la release v1.0.0 (PR #58, trae `V18`) y luego la PR #56 (`V24`, PAR-14 y PAR-12). Confirmar con T07 el renombre `promedio → average`.
2. Cada dev valida su `dev-XX.md` con la checklist de 5 puntos (§12 de `tareas-sprint2.md`).
3. Se mandan las solicitudes de contrato C1, C2, C3, C5 y C6.
4. Ana corrige Taiga (`auditoria-sprint1.md` §5) y crea el esqueleto de las páginas "G06 - …" en la wiki con la plantilla de la cátedra.
5. Se abren las PRs de las ramas ya terminadas (#3512 y #291) y se cierran las superadas.
