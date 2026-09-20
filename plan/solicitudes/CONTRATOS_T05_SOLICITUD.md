# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 05 (Desafíos Prácticos)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Desafíos Prácticos (Tema 05)
> **Propósito:** acordar (a) los **eventos de entregas/resultados** que el Backoffice consume y (b) que consuman los **PAR-19/20** desde el registro del Backoffice.
> **Envelope (Drive oficial):** `EventoDTO{eventId, eventType, timestamp, producer, payload}` (5 campos) + headers (`traceparent`, `X-Request-Id`, `correlationId`, `actorId`, `role`). Identificadores en inglés.

---

## 1 · Entregas / resultados (lo que el Backoffice necesita de T05)

**Lo que necesitamos:** los eventos de **entrega y resultado/corrección** de los desafíos prácticos, para nuestros read models de engagement y reportes.

**Confirmación que pedimos:**

1. ¿Qué **topic** emiten para entregas/resultados? (sugerido: `practical.challenges`).
2. ¿Qué **eventos** emiten? (sugerido: `SUBMISSION_SUBMITTED`, `SUBMISSION_GRADED`).
3. **Payload** que necesitamos (sugerido):

```json
{
  "submissionId": "sub-1234",
  "challengeId": "pra-prog4-001",
  "studentId": "alu-9843",
  "courseId": "cur-2026-2w2",
  "result": "PASSED",
  "submittedAt": "2026-09-20T15:30:00Z",
  "gradedAt": "2026-09-20T16:10:00Z",
  "lateSubmission": true,
  "feedbackSummary": "Resolución correcta con observaciones menores"
}
```

4. ¿El evento va con el **`EventoDTO`** de 5 campos + headers (`traceparent`, `X-Request-Id`, `correlationId`, `actorId`, `role`)?

## 2 · PAR-19 / PAR-20 (lo que el Backoffice les provee)

Los **PAR-19/20** son **candidatos** del Backoffice (a validar con la cátedra):

| PAR | Concepto | Valor de referencia | Clave sugerida |
|---|---|---|---|
| **PAR-19** | Penalidad por entrega tardía | 30% | `late_submission_penalty_pct` |
| **PAR-20** | Ventana de gracia para entrega tardía | 48 h | `late_submission_window_hours` |

**Confirmación que pedimos:**

1. ¿Consumen **PAR-19/20**? Si sí, los van a leer del **registro del Backoffice** (`GET /api/administration/parameters` + evento de cambio), no hardcodeados.
2. ¿Los valores de referencia les cierran, o proponen otros?
3. **PAR-20**: T03 lo tiene "en suspenso" — ¿ustedes lo usan sí o sí?

## 3 · Formato de respuesta

```markdown
## Contrato — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **Topic:** ... | **eventType:** ... | **Envelope/headers:** ...
- **Payload:** ...
- **Notas:** ...
```

¡Gracias! Con esto cerramos el contrato de entregas/resultados y los PAR que consumen de nosotros.