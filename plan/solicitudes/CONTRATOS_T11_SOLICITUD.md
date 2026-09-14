# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 11 (Social y Notificaciones)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Social y Notificaciones (Tema 11)
> **Propósito:** (a) alinear la **convención de eventos** de la plataforma y (b) acordar cómo el Backoffice **emite avisos/alertas** hacia el sistema de notificaciones.

---

## 0. Contexto

- **T08 (Banco)** nos indicó que el **naming/convención de eventos** lo define **T11** (no se cierra bilateral). Necesitamos conocer la convención oficial para `{dominio}.events`.
- El Backoffice **publica** eventos de aviso/notificación que T11 debe consumir (frescura, riesgo, umbrales, exportación lista).

## 1. Convención de eventos de la plataforma

**Confirmación que pedimos:**
1. ¿Cuál es la **convención oficial** de topics/eventos? (ej. `{dominio}.events`, versionado, partición por clave).
2. **Envelope estándar:** confirmen el formato base. Nosotros usamos `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}` (en curso con T01).
3. ¿Los topic names de **`bank.events`**, **`roadmap.events`** y **`administration.events`** los definen ustedes o cada dominio? (Banco dice que ustedes fijan la convención).

## 2. Eventos de aviso/notificación que el Backoffice emite → T11

El Backoffice **produce** los siguientes avisos que T11 debería consumir:

| Evento propuesto | Cuándo se emite | Datos mínimos |
|---|---|---|
| `DataStaleDetected` | Reportes desactualizados (>15 min, US-10) | `courseId`, `reportType`, `minutesStale` |
| `DataFreshnessRestored` | El tema vuelve a enviar datos (US-10) | `courseId`, `reportType` |
| `StudentAtHighRisk` | Alumno pasa a riesgo ROJO (US-12) | `courseId`, `studentId`, `riskLevel` |
| `ThresholdBreached` | Indicador bajo el umbral configurado (US-14) | `indicator`, `value`, `threshold` |
| `ExportReady` | Exportación asíncrona lista (US-09) | `exportId`, `courseId`, `urlExpiresAt` |

**Confirmación que pedimos:**
1. ¿Consumen estos eventos/avisos? ¿En qué **topic** (`{dominio}.events` de Backoffice) o vía **endpoint**?
2. ¿El **payload** propuesto les cierra, o esperan otro formato (ej. con `notificationType`)?
3. ¿Emisión por **evento (Kafka)** o por **REST a T11**? (preferimos eventos si su convención lo soporta).

## 3. Parámetros (si aplica)

¿Hay **algún PAR-01..23** del Backoffice que T11 deba consumir (ej. umbrales de notificación)? Si no, no aplica.

## 4. Formato de respuesta

```markdown
## Contrato — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **Convención/topic:** ... | **Envelope:** ...
- **Payload/endpoint:** ...
- **Notas:** ...
```

¡Gracias! Con esto alineamos la convención de eventos y el canal de avisos del Backoffice.