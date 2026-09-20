# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 02 (Cursos y Matrícula)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Cursos y Matrícula (Tema 02)
> **Propósito:** definir los contratos de integración con **Cursos** en las **dos direcciones**: el Backoffice **consume** cohorte/matrícula/pertenencia y **encuestas anónimas (CSAT)**, y Cursos **aplica** el PAR-18 (mínimo de respuestas) del Backoffice.
> **Cómo usar este documento:** es una **solicitud**; respondan marcando opciones y completando tablas.

---

## 0. Contexto

El **Backoffice (Tema 12)** es **consumidor puro**. De **Cursos (Tema 02)** consume:

1. **Cohorte / `course_id`** (clave de multitenancy y ámbito de reportes).
2. **Pertenencia del PROFESOR a la cohorte** (matrícula) → para autorizar reportes/métricas.
3. **Padrón / matrícula** (`RosterUpdated`) → para métricas de curso.
4. **Encuestas anónimas (CSAT)** — ahora responsabilidad de Cursos (antes Teóricos/Encuestas).

## 1. Cohorte, matrícula y pertenencia docente

**Confirmación que pedimos:**

| Dato | Mecanismo propuesto | Endpoint/evento |
|---|---|---|
| Cohorte (`course_id`) | REST | `GET /api/courses/{courseId}` |
| Pertenencia del PROFESOR a la cohorte | REST | `GET /api/courses/{courseId}/teacher-membership` → `{isMember}` |
| Padrón/matrícula | Evento | `RosterUpdated` en **`cursos.ciclo-vida`** (topic Drive) |
| Ciclo de vida de la cohorte | Evento | `CourseCreated`, `CourseActivated`, `CourseArchived` |

- Los IDs (`courseId`, `teacherId`) son los **UUIDs de plataforma** (T01/T02).
- Acceso de Backoffice: token **MS** service-to-service vía gateway (`/api/courses/**`).

**Envelope estándar (Drive oficial):** **`EventoDTO{eventId, eventType, timestamp, producer, payload}`**; `correlationId/actorId/role` → headers de Kafka; `eventType` en español.

## 2. Encuestas anónimas (CSAT) — ahora de Cursos

**Confirmación que pedimos:**

1. **Mecanismo:** ¿publican agregados de encuestas por evento (`survey.events`) o por **lectura REST** (`GET /api/courses/{courseId}/survey/summary`)? Proponemos eventos para actualización + REST para replay de read models.
2. **Qué consumir (agregados):** **CSAT por cohorte** (promedio 5 estrellas) y por ítem · **volumen de respuestas** (para el umbral de anonimato) · filtro por `courseId` y rango de fechas.
3. **Anonimato:** solo agregados; ninguna operación reconstruye **autor ↔ respuesta** (RF-ENC-04/12).

**Payload propuesto (agregado CSAT por cohorte):**

```jsonc
{
  "eventId": "uuid",
  "eventType": "SurveyMetricsUpdated",
  "occurredAt": "2026-09-18T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "role": "MS",
  "source": "courses-service",
  "payload": {
    "courseId": "course-10",
    "periodFrom": "2026-08-01",
    "periodTo": "2026-09-18",
    "csatAvg": 4.3,
    "responseCount": 28,
    "dimensions": { "claridad": 4.1, "material": 4.4 }
  }
}
```

## 3. PAR-18 (mínimo de respuestas) que Cursos aplica

| PAR | Concepto | Valor de referencia |
|---|---|---|
| **PAR-18** | Mínimo de respuestas para mostrar métricas CSAT | 5 |

**Mecanismo:** publicamos `GlobalConfigurationChanged` (`{key, value, version}`) en `administration.events` (Outbox, idempotencia por `event_id` + versión). **Caché local con TTL 10 min** + invalidación por evento.

**Confirmación:** ¿Cursos **consume PAR-18** (bloquea CSAT con < N respuestas → "muestra insuficiente")? ¿Otro PAR-01..23 que deba leer?

## 4. Frescura e idempotencia

- Read models del Backoffice: **frescura ≤ 15 min** (decisión de arquitectura).
- Idempotencia por `event_id` + versión; reconstrucción por **replay vía REST**.

## 5. Formato de respuesta

```markdown
## Contrato — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **Mecanismo/topic/endpoint:** ...
- **Payload/schema:** ...
- **PAR que consume:** ... | **Caché TTL:** ...
- **Notas:** ...
```

¡Gracias! Con esto cerramos cohorte/matrícula/pertenencia y las encuestas anónimas (CSAT).