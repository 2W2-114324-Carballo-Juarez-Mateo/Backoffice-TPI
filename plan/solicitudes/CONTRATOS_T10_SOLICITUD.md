# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 10 (Roadmap y Progreso)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Roadmap y Progreso (Tema 10)
> **Propósito:** Definir los contratos de integración que el Backoffice **consume** del Tema 10 para construir sus reportes, métricas y observabilidad.
> **Cómo usar este documento:** es una **solicitud**. Por favor, respondan cada contrato completando las tablas y marcando opciones. Al final hay una plantilla de respuesta.

---

## 0. Contexto (una pantalla)

El **Tema 12 (Backoffice)** es **consumidor puro**: no implementa progreso, XP ni niveles. Los **lee del Tema 10** para construir los read models de reporting (KPIs de observabilidad, panel del profesor, reportes analíticos). Necesitamos acordar, en orden de prioridad:

1. **Eventos de progreso** (`roadmap.events`) — bloquea la actualización de read models.
2. **Contrato REST de lectura** (`/api/roadmap/**`) — para reconstruir read models.
3. **Promoción/abandono y alumno en riesgo** — definición de métricas y umbrales.
4. **Frescura ≤ 15 min + idempotencia**.
5. **PAR-21** (multiplicador) que ustedes consumen de nosotros.

---

## 1. Eventos de progreso (`roadmap.events`)

Proponemos consumir los eventos de progreso del alumno para mantener nuestros read models al día.

**Eventos que nos interesan (a confirmar el nombre real):**

| Evento propuesto | Qué representa | Datos mínimos sugeridos |
|---|---|---|
| `ProgressUpdated` | Avance de un alumno en un curso | `courseId`, `studentId`, `progressPct`, `level`, `xp`, `occurredAt` |
| `LevelUp` | Subida de nivel | `courseId`, `studentId`, `newLevel`, `previousLevel`, `occurredAt` |
| `MilestoneReached` (si aplica) | Hito/objetivo logrado | `courseId`, `studentId`, `milestoneId`, `occurredAt` |

**Envelope estándar de plataforma** (propuesto, igual al de los demás temas):

```json
{
  "eventId": "uuid",
  "eventType": "ProgressUpdated",
  "occurredAt": "2026-09-12T14:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "role": "MS",
  "source": "roadmap-service",
  "payload": {
    "courseId": "course-10",
    "studentId": "student-42",
    "progressPct": 62.5,
    "level": 3,
    "xp": 1250
  }
}
```

> `role` se agrega al envelope como estándar de plataforma (en curso con T01).

**Por favor confirmen:**
- ¿El topic es `roadmap.events`? ¿Qué eventos publican realmente y con qué payload versionado?
- ¿Los IDs reutilizan los de plataforma (`courseId`/`studentId` UUID)?
- ¿Con qué frecuencia emiten progreso (por cambio, por día, batch)? (impacta nuestra frescura ≤ 15 min).

---

## 2. Contrato REST de lectura (`/api/roadmap/**`)

Según la convención de rutas de la plataforma, toda API vive bajo `/api/{servicio}/**`. Asumimos su prefijo `/api/roadmap/**`. Necesitamos lecturas para **reconstruir read models** (replay) y para cálculos que no viajan por evento.

**Endpoints propuestos (a validar):**

| Endpoint (propuesta) | Método | Qué devuelve |
|---|---|---|
| `GET /api/roadmap/courses/{courseId}/progress` | GET | Progreso por alumno de la cohorte (listado) |
| `GET /api/roadmap/courses/{courseId}/students/{studentId}/progress` | GET | Progreso de un alumno |
| `GET /api/roadmap/courses/{courseId}/levels` | GET | Distribución de niveles de la cohorte |
| `GET /api/roadmap/courses/{courseId}/retention` | GET | Métricas de aprobación/abandono y promoción (agregados) |

**Por favor confirmen:**
- URLs reales (bajo `/api/roadmap/**`), métodos, roles requeridos (nosotros consumimos con token `MS` service-to-service vía gateway).
- Paginación (¿`offset`/`limit` o cursor?), filtros soportados (`courseId`, `dateFrom`, `dateTo`), formato de respuesta.
- ¿Existe un endpoint de **métricas agregadas por cohorte** (aprobación/abandono/promoción) o debemos calcularlos del stream de progreso?

---

## 3. Promoción / abandono y alumno en riesgo

Estos son los KPIs que mostramos (hoy con datos mock) y que necesitamos alimentar con datos reales:

- **Aprobación/abandono** por cohorte (referencia mock: 74.8% / 8.4%).
- **Promoción P90** (referencia mock: 8.9%).

**Preguntas:**
1. ¿T10 expone aprobación/abandono/promoción como **agregados** (evento o endpoint), o los **derivamos nosotros** del stream de progreso? Si los derivamos nosotros, ¿qué definición usan para "aprobado"/"abandonado"/"promovido"?
2. **Alumno en riesgo (RF-RPT-03):** en el panel del profesor marcamos alumnos en riesgo. ¿Qué métrica/umbral propone T10 (ej. progreso por debajo de X% en una ventana, sin actividad en N días)? Necesitamos una definición acordada para no divergir entre temas.

---

## 4. Frescura e idempotencia

- Nuestros read models se actualizan por eventos; requerimos **frescura ≤ 15 min** (RF-RPT-06) → si ustedes emiten batch, necesitamos que la frecuencia sea acorde.
- Idempotencia por `event_id` (+ versión): confirmen que cada evento lleva `event_id` único y (si aplica) `version` monótona.
- Read models: **reconstruibles por replay** vía el contrato REST del punto 2 (no dependemos del historial del broker).

---

## 5. PAR-21 (multiplicador de eventos) — ustedes consumen de nosotros

- **PAR-21** `event_multiplier_cap` (techo 3x del multiplicador de eventos/rachas) es un **parámetro global del Backoffice** que ustedes consumen.
- Lo publicamos en `administration.events` como `GlobalConfigurationChanged` (`{key, value, version}`).
- **Confirmación que pedimos:** ¿T10 efectivamente consume ese PAR? ¿Usan caché local con TTL? (nosotros invalidamos con el evento; la caché TTL 10 min es el respaldo ante caída del Backoffice). ¿Hay algún otro PAR de los que tenemos (PAR-01..23) que T10 deba consumir?

---

## 6. Lo que el Backoffice aporta (para acelerar)

- Envelope estándar `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}`.
- Idempotencia por `event_id` + versión en todos los consumidores.
- Convención de topics `{dominio}.events` y rutas `/api/{servicio}/**`.
- Nuestros endpoints de reporting que consumirán estos datos (para que definan el acceso).

---

## 7. Formato de respuesta esperado

Por favor respondan por contrato:

```markdown
## Contrato N — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **Especificación acordada:**
  - Topic / evento: ... | Payload (JSON/OpenAPI): ...
  - Endpoint/URL: ... | Método: ... | Rol: ...
  - Paginación/filtros: ... | Versión del contrato: ...
  - Idempotencia/frescura: ...
- **Notas / decisiones abiertas:** ...
```

**Prioridad para nosotros:** 1 (eventos `roadmap.events`) y 2 (lecturas REST) primero; 3 (promoción/riesgo) y 5 (PAR-21) a continuación; el resto no bloquea.

¡Gracias! Con estas respuestas cerramos los contratos y desbloqueamos el reporting del Backoffice.