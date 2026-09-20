# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 11 (Social y Notificaciones)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Social y Notificaciones (Tema 11) — *dueños del Kafka, de la convención de eventos y del registro de topics*
> **Propósito:** ratificar con ustedes la **convención de eventos** (según su **PDF oficial**) y que **registren los topics del Backoffice** antes de implementar la mensajería (Sprint 1).

---

## 0 · Contexto

- Su **PDF de eventos (2026-09)** fija el estándar: **`EventoDTO{eventId, eventType, timestamp, producer, payload}`** (5 campos, genérico `Event<T>`), **todo en inglés**, `producer = spring.application.name`, serialización `JsonSerializer`/`JsonDeserializer` + consumer tipado, y **no se crean topics nuevos sin avisarles**.
- Como T11 gestiona el Kafka y el catálogo de topics, les pedimos **registrar nuestros topics** y ratificar los que consumimos.

## 1 · Convención — confirmaciones que pedimos

1. **`EventoDTO` / `Event<T>` de 5 campos** (su PDF): ¿lo confirmamos como el envelope obligatorio? ¿`correlationId`/`actorId`/`role` van como **headers de Kafka** (además de `traceparent`, `X-Request-Id`)?
2. **`producer`**: confirmamos que usamos el `spring.application.name` → **`backoffice-service`** (como su ejemplo `challenges-service`).
3. **Serialización**: ¿confirmamos `JsonSerializer`/`JsonDeserializer` + `spring.json.trusted.packages` + consumer tipado `Event<Payload>`?

## 2 · Registro de topics del Backoffice (les pedimos el alta)

| Topic | Uso | Tipo |
|---|---|---|
| `administration.events` | Mutación de configuración global / parámetros | emisión (Backoffice) |
| `audit.events` | Auditoría hacia T01 (T01 persiste) | emisión (Backoffice) |
| `administration.events.dlt` | Dead Letter del outbox (reintentos agotados) | emisión (Backoffice) |

## 3 · Topics que consumimos (ratificar)

- **`challenges.results`** (T03 — resultado de desafío, hecho único).
- **`courses.lifecycle`** (T02 — matrícula/ciclo de vida).
- **`bank.events`** (T08 — saldo) · **`roadmap.events`** (T10 — progreso) · **`identity.events`**/`retention.events` (T01).

## 4 · Eventos que el Backoffice emite → T11

### A registrar/ratificar
- **`ACADEMIC_DATA_EXPIRING`** → **`system.notifications`** (preaviso de vencimiento de datos académicos / retención, PAR-16/17):
  `{cohortId, courseName, closingDate, expirationDate, daysRemaining}`.

### A confirmar con ustedes
¿Consumen estas alertas operativas en `system.notifications` o las descartamos?

| Evento propuesto | Cuándo se emite | Datos mínimos |
|---|---|---|
| `STUDENT_AT_HIGH_RISK` | Alumno pasa a riesgo crítico | `courseId`, `studentId`, `riskLevel` |
| `DATA_STALE_DETECTED` | Reportes desactualizados (>15 min) | `courseId`, `reportType`, `minutesStale` |
| `THRESHOLD_BREACHED` | Indicador bajo el umbral | `indicator`, `value`, `threshold` |
| `EXPORT_READY` | Exportación asíncrona lista | `exportId`, `courseId`, `urlExpiresAt` |

## 5 · Formato de respuesta

```markdown
## Contrato — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **Topic:** ... | **eventType (inglés):** ... | **Envelope/headers:** ...
- **Payload:** ...
- **Notas:** ...
```

¡Gracias! Con esto cerramos la convención, registramos nuestros topics y ratificamos los que consumimos.