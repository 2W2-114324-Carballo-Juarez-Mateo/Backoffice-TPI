# Retrospectiva Scrum — TPI-G06 · Sprint 01

> **Fecha:** 28 de septiembre de 2026 · **Equipo:** TPI-G06 · **Sprint:** 01 (14/09 → 27/09) · **Participantes:** 9
>
> 1. 412085 — Baigorria, Damián Gabriel
> 2. 114324 — Carballo Juarez, Mateo
> 3. 405361 — Cerasulo, Regina Loreta
> 4. 412953 — Cerquatti, Máximo
> 5. 412142 — Cortez, Joaquín
> 6. 404957 — Ducart, Ana Paula
> 7. 114373 — Gianoli, Bruno
> 8. 421349 — Maldonado, Valentina Ariadna
> 9. 111067 — Paz, Luciano

---

## 1 · ¿Qué hicimos bien? — Continuar

| Práctica | Qué pasó |
|---|---|
| **Adaptación y organización** | Nos adaptamos a la salida de tres integrantes y redistribuimos las tareas. |
| **Comunicación y respeto** | Compartimos información y opiniones por todos los medios; el trato fue respetuoso. Sin habernos conocido antes, nos integramos rápido como equipo. |
| **Apoyo entre compañeros** | Asistimos a quienes tenían dudas para que siguieran el ritmo. Hubo un rendimiento parejo: nadie quedó muy atrás ni se adelantó demasiado. |
| **Uso acompañado de IA** | La IA se usó de forma balanceada: cada integrante revisó, integró y corrigió lo que produjo, con criterio propio. |
| **Participación y creatividad** | Todos aportaron ideas y razonamiento propio para construir las soluciones. |

## 2 · ¿Qué hicimos mal? — Dejar de hacer

| Práctica | Qué pasó |
|---|---|
| **Planificar sin ajustar la capacidad** | Subestimamos las historias porque no tuvimos en cuenta la velocidad de trabajo con IA y tomamos menos tareas de las que podíamos completar. |
| **Dejar Taiga sin actualizar** | El tablero no siempre reflejó el avance real. |
| **Trabajar sin acordar el alcance** | Hubo poca coordinación con otros grupos sobre qué nos correspondía; tuvimos que descartar tareas planificadas que eran de otros. |
| **Duplicar tareas** | Algunas funcionalidades se superpusieron entre compañeros por falta de revisión humana del plan y de la división de tareas. |
| **Revisar solo por WhatsApp** | Los comentarios de las PR no quedaron registrados en GitHub. |
| **Demorar el frontend** | El miedo a romper el repositorio compartido retrasó las actualizaciones del front. |

## 3 · ¿Qué deberíamos comenzar a hacer? — Comenzar a hacer

| Práctica | Acuerdo |
|---|---|
| **Estimar con mayor precisión** | Ajustar la carga a nuestra capacidad real, contemplando IA, revisión e integración, y asignar más tareas en este sprint. |
| **Actualizar Taiga** | Mantener las tareas alineadas con su avance. |
| **Coordinar entre grupos** | Acordar responsabilidades, eventos y endpoints antes de implementar. |
| **Revisar la planificación** | Validar los planes generados con IA: tareas coherentes, que no se pisen, división equitativa y con menos dependencias. Más control humano. |
| **Ordenar las PR** | Pedir la PR cuando el trabajo de la rama esté completo; no abrirla con tareas a medias. |
| **Registrar los comentarios en GitHub** | Todo comentario de revisión queda en la descripción o en los comentarios de la PR. |
| **Integrar el frontend a tiempo** | No dejar las tareas de front para el final. |

---

## 4 · Lo que dicen los datos (auditoría del 29/09)

> Sirve para confirmar o matizar lo que sentimos en la retro. Detalle completo en `auditoria-sprint1.md`.

| Dato | Valor | Lectura |
|---|---|---|
| Tareas cerradas en Taiga | **144 / 155 (93 %)** | Programar no fue el problema. |
| Historias cerradas | **6 / 13 (46 %)** | **Cerrar historias sí lo fue.** |
| Story points completados | **19 / 34 (56 %)** | Ídem. |
| PRs del backend | 54: 44 mergeadas, 9 cerradas sin merge, 1 abierta | 3 de las 9 fueron por el prefijo `docs/` (rechazado por el chequeo de nombres). |
| Trabajo terminado que no llegó a `develop` | Guards (#3512), badge de frescura (#291), IT de US-01 | Ramas listas **sin PR**. |
| Trabajo que se pisó con otro o con una decisión de alcance | ~3.500 líneas (golden set local, envelope duplicado, contratos del Drive) | Es la "duplicación" que marcamos en la retro, medida. |
| Historias con estado incoherente en Taiga | 6 | Es el "Taiga sin actualizar", medido. |
| Contratos de lectura firmados | 1 cerrado (T01), 2 con acuerdo (T03, T08), el resto sin firma; **T05 sin solicitud** | Es el "trabajar sin acordar el alcance", medido. |

**Matiz importante sobre "subestimamos":** es cierto que con IA terminamos las **tareas** más rápido de lo previsto (93 %). Pero solo cerramos el 46 % de las **historias**, porque cerrarlas dependía de otros equipos, de PRs que no se abrieron y de tareas reabiertas. **Tomar más tareas no alcanza: hay que planificar para cerrar historias.** Por eso el plan del Sprint 2 sube el compromiso (48 SP en el núcleo contra 34 del S1) y, a la vez, pone los contratos y el cierre del S1 primero.

---

## 5 · De la retro al plan: cómo se implementa cada acuerdo

| Acuerdo | Mecanismo concreto en `tareas-sprint2.md` | Responsable | Cómo lo medimos en la retro del S2 |
|---|---|---|---|
| Estimar con mayor precisión | Sin horas: SP por historia y reparto pareja en líneas de código efectivas por persona (§10). Núcleo / con gate / stretch (D-10) | Todos | % de historias del núcleo cerradas (objetivo ≥ 80 %) |
| Asignar más tareas | Núcleo de 48 SP (vs 34 del S1) + 21 SP Should + 8 SP stretch | Todos | SP completados vs S1 |
| Actualizar Taiga | Corrección de estados en el CP0 (H-07); cada dueño mueve su tarjeta al abrir y al mergear la PR | Ana + cada dev | Historias con estado incoherente al cierre = 0 |
| Coordinar entre grupos | HT01 como historia **Must** con responsable por tema (§3); gate en el CP2 con fallback | Máximo, Mateo, Damián, Luciano, Valentina, Bruno · seguimiento en Taiga: Ana | Contratos de los 6 temas fuente con firma |
| Revisar la planificación | Checklist de 5 puntos que cada dev confirma sobre su `dev-XX.md` antes del CP1 (§12); correcciones con evidencia (`correcciones-propuesta.md`) | Cada dev | Tareas reasignadas a mitad de sprint por pisarse = 0 |
| Menos dependencias | PR de contratos compartidos S2-00 congelado; un dueño por paquete (D-02, D-03) | Luciano + revisores | Conflictos de merge entre slices del equipo |
| Ordenar las PR | Una rama por slice; PR solo con la rama terminada, sincronizada con `develop` y con el verify pegado (§8) | Cada dev | Ramas con trabajo terminado sin PR > 2 días = 0 |
| Comentarios en GitHub | Etiquetas `bloqueante:`/`sugerencia:`/`pregunta:`/`nit:` y "aprobado sin ejecutar" no cuenta (`revision-pr.md` §1) | Revisores | PRs mergeadas sin ningún comentario de review = 0 |
| Frontend a tiempo | Cada historia con FE tiene su tarea FE en el CP3 o CP4; las ramas FE terminadas (#3512, #291) van en el CP0 | Dueños de FE | Tareas FE que quedan para el CP5 (solo el builder de US-15) |
| Uso acompañado de IA (continuar) | Cada PR la revisa y ejecuta un humano distinto al autor (matriz §9) | Revisores | — |

---

## 6 · Acuerdos de trabajo para el Sprint 2

1. **Primero cerramos lo del Sprint 1** (CP0), después arrancamos lo nuevo.
2. **Nadie programa contra un contrato sin firmar** sin puerto, flag y fallback.
3. **Cada archivo tiene un dueño.** Si necesito cambiar algo de otro, se lo pido en su PR o por issue.
4. **La PR se abre con la rama terminada**, sincronizada con `develop` y con el verify pegado.
5. **Los comentarios de review viven en GitHub.**
6. **Taiga lo mueve el dueño** de la tarea, al abrir y al mergear la PR.
7. **Si un plan lo generó la IA, lo valida una persona** antes de repartirlo (checklist §12).

---

## 7 · Próxima retro

Al cierre del Sprint 2 (11/10/2026) revisamos las métricas de la columna "Cómo lo medimos" de §5 y comparamos contra la tabla de §4.
