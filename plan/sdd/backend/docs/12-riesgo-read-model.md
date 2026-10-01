# Riesgo por cohorte (HU11) — reglas y read model

> Tarea: **#307** (documentación) · Historia: **HU11 #25** · Fuente: `tareas-sprint2.md` §6.1 ·
> Código: #303/#304 (Damián) · #305 (Valentina) · #306 (Regina) · #308 (Máximo).

## Objetivo

Documentar las **reglas de riesgo** y el **esquema del read model por cohorte** que alimentan el
panel del docente (HU12), la alerta `STUDENT_AT_HIGH_RISK` (HU12 #312) y los reportes. El riesgo se
calcula **por cohorte** sobre el read model de `reporting`, con **RLS por curso** (V20).

## Regla de riesgo (se evalúa en este orden; gana la primera que aplica)

| Orden | Estado | Condición |
|---|---|---|
| 1 | **RED** | inactividad **> 10** días **o** (≥ 3 intentos **y** reprobación **> 60 %**) **o** vidas agotadas (flag) |
| 2 | **YELLOW** | inactividad **5–10** días **o** (≥ 3 intentos **y** reprobación **40–60 %**) |
| 3 | **GREEN** | (≥ 3 intentos **y** aprobación **≥ 70 %**) **o** (< 3 intentos: las tasas no se evalúan) |
| 4 | **YELLOW** | **R-1:** cualquier otro caso (≥ 3 intentos con aprobación < 70 % y reprobación < 40 %) |

### Decisiones del grupo (29/09)

- **R-1 — el hueco va a YELLOW.** El caso de la regla de Taiga que quedaba sin clasificar
  (aprobación 60–70 % con menos de 5 días de inactividad) se clasifica **YELLOW**: se prefiere
  avisar de más.
- **R-2 — mínimo 3 intentos.** Con menos de 3 intentos **no se calculan las tasas** (ni para bajar
  a RED/YELLOW ni como requisito de GREEN): el estado sale solo de la inactividad y de las vidas, y
  el panel muestra el factor **"muestra insuficiente"**. Así un alumno que reprobó su único intento
  no queda en rojo.

### Configuración tipada (`reporting.risk.*`)

Umbrales en `@ConfigurationProperties("reporting.risk")`, **no** en PAR:

| Propiedad | Valor |
|---|---|
| `red-inactivity-days` | 10 |
| `yellow-inactivity-days` | 5 |
| `red-failure-rate` | 60 |
| `yellow-failure-rate` | 40 |
| `green-approval-rate` | 70 |
| `min-attempts` | 3 |
| `lives-exhausted.enabled` | false |

## Casos de prueba de la regla (#306)

Los 12 casos (valores límite + decisiones) que debe cubrir la suite de `#306`:

| Caso | Intentos | Aprobación / reprobación | Inactividad | Esperado |
|---|---:|---|---:|---|
| Límite de inactividad RED | 5 | 80 % / 20 % | 10 → 11 días | YELLOW → **RED** |
| Límite de inactividad YELLOW | 5 | 80 % / 20 % | 4 → 5 días | GREEN → **YELLOW** |
| Límite de reprobación RED | 100 | 40 % / 60 % → 39 % / 61 % | 1 día | YELLOW → **RED** |
| Límite de reprobación YELLOW | 100 | 61 % / 39 % → 60 % / 40 % | 1 día | YELLOW (R-1) → **YELLOW** (regla) |
| Límite de aprobación GREEN | 100 | 69 % / 31 % → 70 % / 30 % | 1 día | YELLOW (R-1) → **GREEN** |
| R-1 | 3 (2 aprobados) | 67 % / 33 % | 2 días | **YELLOW** |
| R-2 bajo el mínimo | 2 (0 aprobados) | 0 % / 100 % | 1 día | **GREEN** + "muestra insuficiente" |
| R-2 en el mínimo | 3 (1 aprobado) | 33 % / 67 % | 1 día | **RED** |
| Precedencia | 10 (9 aprobados) | 90 % / 10 % | 14 días | **RED** (la inactividad gana) |
| Escenario 1 de Taiga | 5 | 40 % / 60 % | 14 días | **RED** |
| Escenario 2 de Taiga | 5 | 80 % / 20 % | 7 días | **YELLOW** |
| Escenario 3 de Taiga | 5 | 80 % / 20 % | 1 día | **GREEN** |

## Read model de cohorte (V19, esquema `reporting`)

Migración `V19__reporting_cohort_read_model.sql` (#303, Damián): único `(course_id, student_id)`,
índices por `course_id` y `(course_id, risk_level)`. Los tipos concretos y restricciones los define
la migración V19 (CP2); este documento fija la estructura acordada.

### `cohort_roster`

| Columna | Descripción |
|---|---|
| `course_id` | curso de la cohorte (clave con `student_id`) |
| `student_id` | alumno del padrón (T02 `ROSTER_UPDATED`) |
| `enrolled_at` | momento de inscripción |
| `source` | origen del padrón |

### `student_activity_summary`

| Columna | Descripción |
|---|---|
| `course_id`, `student_id` | clave única (cohorte + alumno) |
| `last_activity_at` | último evento de actividad |
| `attempts`, `passed`, `failed` | contadores de desafíos (T03) |
| `lives_exhausted` | flag de vidas agotadas (T10, detrás de flag) |
| `risk_level` | `RED` / `YELLOW` / `GREEN` |
| `risk_factors` | factores que dispararon el estado (muestra insuficiente, inactividad, tasas, vidas) |
| `risk_computed_at` | instante del recálculo |
| `previous_risk_level` | estado anterior (para la transición a RED) |

## Ciclo de vida y consumo

1. **Proyector (#305, Valentina):** lee `reporting.ingested_event` (T03 `CHALLENGE_COMPLETED`,
   T02 `ROSTER_UPDATED`; T05/T10 detrás de flag), idempotente, con checkpoint, cada ≤ 5 min
   (siempre menor que PAR-23).
2. **Clasificador (#304, Damián):** puro (sin Spring), recalcula `risk_level` con la regla de arriba;
   umbrales de `reporting.risk.*`; el factor "vidas agotadas" detrás de
   `reporting.risk.lives-exhausted.enabled=false`.
3. **RLS (#311, Máximo):** `V20` con `ENABLE` **y** `FORCE ROW LEVEL SECURITY`; `TenantContext`
   (`SET LOCAL app.current_course`); guardia anti-comparación.
4. **Panel (#310, Regina) / semáforo FE (#313, Luciano):**
   `GET /api/backoffice/reports/courses/{courseId}/teacher` con alumnos, semáforo (color + texto),
   factores y "muestra insuficiente"; `DataFreshnessDto`; < 2 s.
5. **Alerta (#312, Damián):** `STUDENT_AT_HIGH_RISK` por `DomainEventOutbox` **solo en la transición
   a RED** (misma transacción que el recálculo) a `notifications.events` (payload con IDs, sin PII).

> **Sin padrón (T02) no se ven los inactivos que nunca actuaron:** sin eventos no hay fila. El
> padrón de T02 es ruta crítica (§3 del plan).

## Referencias

- `tareas-sprint2.md` §6.1 (HU11) · `dev-03.md` (#303/#304) · `dev-05.md` (#305) · `dev-07.md`
  (#306) · `revision-pr.md` (checklist) · `correcciones-propuesta.md` (R-1, R-2, P-12).
- Taiga: #303, #304, #305, #306, #307, #308, #310, #311, #312, #313.