# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 11 (Social y Notificaciones)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Social y Notificaciones (Tema 11) — *dueños del Kafka y de la convención de topics*
> **Propósito:** ratificar con ustedes la **convención de eventos** (basada en el **Drive oficial**) y los **topics/eventos** que el Backoffice emite y consume, antes de implementar la capa de mensajería (Sprint 1).

---

## 0. Contexto

- El **Drive oficial (2026-09)** fija el estándar: **`EventoDTO{eventId, eventType, timestamp, producer, payload}`** (5 campos), `eventType` y topics en **español**, `producer = tema-XX-nombre`, `correlationId/actorId/role` como **headers de Kafka**.
- Como T11 gestiona el Kafka y el catálogo de topics, **todos nuestros topics/eventos se ratifican con ustedes antes de implementarse**.

## 1. Convención de eventos — confirmaciones que pedimos

1. **`EventoDTO` de 5 campos** (Drive): ¿lo confirmamos como el envelope obligatorio para todo evento de la plataforma? ¿`correlationId`/`actorId`/`role` van como **headers de Kafka** (así lo entendemos), o hay algún header adicional (ej. `traceparent`, `X-Request-Id`)?
2. **Nuestros topics de emisión** — necesitamos el nombre oficial (en español) para:
   - Mutación de configuración global / parámetros (antes `administration.events`).
   - Auditoría hacia T01 (antes `audit.events`; T01 persiste).
   - Avisos de Backoffice hacia notificaciones (ver §2).
3. **Nuestros topics de consumo** — confirmar que consumimos:
   - **`desafios.resultados`** (T03 — hecho único de resultado de desafío).
   - **`cursos.ciclo-vida`** (T02 — matrícula/ciclo de vida).
   - Otros que T11 publique como habilitados por release (lista de canales).
4. **`eventType` en español**: confirmar la convención de nombres (SCREAMING_SNAKE_CASE en castellano) para nuestros eventos de configuración/auditoría.

## 2. Eventos que el Backoffice emite → T11

### Confirmado por el Drive
- **`VENCIMIENTO_DATOS_ACADEMICOS`** → **`sistema.notificaciones`** (preaviso de vencimiento de datos académicos / retención, PAR-16/17):
  `{cohortId, courseName, closingDate, expirationDate, daysRemaining}`.

### A confirmar con ustedes
El Backoffice también puede emitir alertas operativas. ¿Las consumen en `sistema.notificaciones` o las descartamos?

| Evento propuesto | Cuándo se emite | Datos mínimos |
|---|---|---|
| `StudentAtHighRisk` | Alumno pasa a riesgo académico crítico (US-12) | `courseId`, `studentId`, `riskLevel` |
| `DataStaleDetected` | Reportes desactualizados (>15 min) | `courseId`, `reportType`, `minutesStale` |
| `ThresholdBreached` | Indicador bajo el umbral configurado (US-14) | `indicator`, `value`, `threshold` |
| `ExportReady` | Exportación asíncrona lista | `exportId`, `courseId`, `urlExpiresAt` |

**Preguntas:**
1. ¿`VENCIMIENTO_DATOS_ACADEMICOS` va sí o sí a `sistema.notificaciones`, o a un topic de Backoffice que ustedes registren?
2. ¿Las alertas operativas (`StudentAtHighRisk`, `DataStaleDetected`, `ThresholdBreached`, `ExportReady`) entran en su catálogo? ¿En qué topic?
3. ¿Los eventos van con el **`EventoDTO`** + headers (`traceparent`, `X-Request-Id`, `correlationId`, `actorId`, `role`)?

## 3. Formato de respuesta

```markdown
## Contrato — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **Topic:** ... | **eventType (español):** ... | **Envelope/headers:** ...
- **Payload:** ...
- **Notas:** ...
```

¡Gracias! Con esto cerramos la convención y ratificamos nuestros topics/eventos antes de implementar la mensajería.