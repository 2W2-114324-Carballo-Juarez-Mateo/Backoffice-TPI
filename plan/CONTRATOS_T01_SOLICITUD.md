# Propuestas Tema 12 (Backoffice) → Tema 01 (Usuarios) — Contratos pendientes

> **En respuesta a:** su contestación de la primera ronda.
> **Alcance:** solo los puntos que quedaron "Requiere ajuste". Cada propuesta es nuestra postura inicial; esperamos confirmación o corrección.

---

## 1 · Alcance global `ALL` (Contrato 2)

**Propuesta de resolución — no hace falta un header nuevo:**

- El gateway ya propaga `X-User-Roles` con el rol **validado** (su `IdentityPropagationFilter` borra headers entrantes y los inyecta desde el token → anti-spoofing).
- El Backoffice **deriva el alcance server-side**: para endpoints globales (`/api/reports/platform`, comparativas entre cursos), si `X-User-Roles` contiene `ADMIN`, el `TenantContext` setea `app.current_course = 'ALL'`. **Nunca** se toma alcance del request: un PROFESOR/ALUMNO que intente alcance global recibe `403`.
- Las lecturas globales se **auditan** (queda atado al Contrato 4).

**Confirmación que pedimos:**
1. ¿El rol `ADMIN` en `X-User-Roles` es garantía suficiente para que el Backoffice derive `ALL`? ¿O necesitan un mecanismo adicional (ej. permiso `REPORTS_VIEW_ALL` propagado)?

---

## 2 · Auditoría (Contrato 4)

**2.1 Publicación (nosotros → T01)**

- **Topic propuesto:** `audit.events` (v1).
- **Envelope:** el estándar ya acordado (`eventId, eventType, occurredAt, correlationId, actorId, source, payload`), con `role` agregado.
- **Eventos que el Backoffice emitirá:**
  - `ParameterChanged` (alta/modificación de parámetro global)
  - `ModelProviderChanged` (alta/baja/sustitución de proveedor LLM)
  - `EvaluatorActivated` (activación de evaluador único)
  - `GlobalRead` (lecturas con alcance `ALL`)
- **Schema de ejemplo:**

```json
{
  "eventId": "uuid",
  "eventType": "ParameterChanged",
  "occurredAt": "2026-09-12T14:00:00Z",
  "correlationId": "uuid",
  "actorId": "user-123",
  "role": "ADMIN",
  "source": "administration-service",
  "payload": {
    "operation": "UPDATE_PARAMETER",
    "resource": "PAR-01",
    "result": "OK",
    "reason": null
  }
}
```

- **Garantía:** estos eventos se persisten en la **misma transacción** del cambio (tabla `outbox_message` del administration-service) y un poller los publica — mismo mecanismo que usan ustedes.

**2.2 Lectura (T01 → nosotros)**

| Endpoint | Método | Rol | Propuesta |
|---|---|---|---|
| `GET /api/audit` | GET | ADMIN | Paginado: `resource`, `actorId`, `eventType`, `from`, `to`, `limit` (def. 50/máx 100), `offset`. Respuesta `{items, total, nextOffset}` |
| `GET /api/audit/{id}` | GET | ADMIN | Detalle del evento |

**Confirmación que pedimos:** ¿topic `audit.events` + schema de arriba les cierran? ¿Paginación por `offset` o prefieren cursor? ¿Roles de lectura = solo `ADMIN`?

---

## 3 · Retención (Contrato 5)

**Acuerdo de base:** la responsabilidad de borrar/anonimizar es **100% de T01**; el Backoffice **nunca purga por su cuenta** — solo **alinea sus read models** de reporting al recibir los eventos.

**3.1 Payload mínimo propuesto**

Para que podamos identificar exactamente qué read model tocar:

```jsonc
// DataAnonymized
{
  "eventId": "uuid",
  "eventType": "DataAnonymized",
  "occurredAt": "2026-09-12T14:00:00Z",
  "correlationId": "uuid",
  "actorId": "user-123",
  "source": "users-service",
  "payload": {
    "entityType": "COURSE",          // COURSE | USER | USER_SNAPSHOT
    "entityId": "course-10",          // courseId o userId (los mismos IDs de plataforma)
    "action": "ANONYMIZED",
    "reason": "retencion-vencida"
  }
}
```

```jsonc
// RetentionDecisionCreated
{
  "eventId": "uuid",
  "eventType": "RetentionDecisionCreated",
  "occurredAt": "2026-09-12T14:00:00Z",
  "correlationId": "uuid",
  "actorId": "user-123",
  "source": "users-service",
  "payload": {
    "entityType": "COURSE",
    "entityId": "course-10",
    "decision": "ANONYMIZE",         // EXTEND | ANONYMIZE | PENDING
    "decidedAt": "2026-09-12T14:00:00Z",
    "expiresAt": null
  }
}
```

**Confirmación que pedimos:** ¿`entityType`/`entityId` reutilizan los IDs de plataforma (`courseId` UUID, `userId` UUID)? ¿Quieren agregar campos al payload?

**3.2 Contrato de lectura de política/estado (opcional, bajo impacto):**

| Endpoint | Método | Rol | Propuesta |
|---|---|---|---|
| `GET /api/retention/policy` | GET | ADMIN | Política vigente (plazo configurable) |
| `GET /api/retention/records?entityType=&entityId=` | GET | ADMIN | Estado de retención de un registro |

Si prefieren postergarlo, no nos bloquea: con los eventos alcanza para alinear read models.

---

## 4 · Estado 2FA (Contrato 7)

- El front hoy muestra "2FA Verificado" con **dato mock** → no bloquea la integración.
- **Propuesta a futuro:** cuando definan el mecanismo (TOTP u otro), propagar un claim en el token (ej. `"2fa": true`) o un header. El Backoffice **no decide** el mecanismo; solo consume el estado para UI.
- **Confirmación que pedimos:** ¿claim vs endpoint `GET /api/auth/session`? (lo puede decidir T01; nosotros nos adaptamos).

---

## 5 · PAR-24 (Contrato 8)

- **Aceptado:** ownership 100% de T01. Lo **quitamos de nuestros PAR candidatos** (quedamos con `PAR-01..23`).
- Si más adelante necesitan que el valor sea consultable como parámetro global, lo coordinamos; no asumimos nada hoy.

## 6 · Trazabilidad

- Alineados: usaremos `traceparent` (W3C) + `X-Request-Id` en logs. No requiere acción de ustedes.

---

## Formato de respuesta esperado

Por cada propuesta, confirmar o corregir:

```markdown
## <nombre del contrato>
- **¿Aceptan la propuesta?** SÍ / NO / Requiere ajuste
- **Corrección / valor final:** ...
- **Pendiente:** ...
```

Prioridad de nuestro lado: **1 (ALL)** y **2 (auditoría)** primero; **3 (retención)** a continuación; el resto no bloquea.