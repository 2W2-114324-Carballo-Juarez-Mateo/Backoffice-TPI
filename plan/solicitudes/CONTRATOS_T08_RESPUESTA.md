# Respuesta — Backoffice (T12) → Banco (T08)

> **En respuesta a:** su ronda de confirmación de `CONTRATOS_T08_SOLICITUD.md`.

---

## Sección 1 — Respuesta técnica (para la IA / integración)

### 1. Read models — nos suscribimos a tu evento ✅
Decisión tomada: además del **REST** (`/api/bank/**`) para **replay/inicial**, vamos a **suscribirnos** a tu evento de **actualización de saldo por alumno/curso** (mejor en recursos y frescura, como recomendás).

Para cerrar el contrato del evento, necesitamos que confirmen:
- **Nombre del topic** (¿`bank.events`?).
- **Payload** sugerido: `{courseId, studentId, balance, delta, currency, occurredAt}` (+ envelope estándar `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}`).
- **Idempotencia:** `event_id` + versión.

> **⏳ PENDIENTE DE CONFIRMAR:** este evento de **actualización de saldo/vidas por alumno** es el que aún falta cerrar con Banco (nombre del topic + payload final). Es el "otro parámetro" de vidas pendiente.

### 2. distribution — ✅ Confirmado
A Banco le pedimos **saldo en monedas**; el **XP lo expone Roadmap (T10)**.

### 3. "Retención vs desafíos" — ✅ Entendido
**Retención de monedas → Banco** · **Retención de XP → Roadmap (T10)**. El Backoffice **integra ambos** en el agregado.

### 4. Naming de canales — ✅ De acuerdo
Esperamos la **lista de canales habilitados por release** que publique **Notificaciones (T11)** para alinear el topic del evento de saldo.

---

## Sección 2 — Respuesta simple (para Aylen 😊)

Hola Aylen, acá va lo que quedamos:

1. **Cómo leemos tus datos:** decidimos **escuchar tu evento** (además de poder consultarte por REST). Así actualizamos el saldo **por alumno y por curso** casi en tiempo real y gastamos menos recursos. Solo necesito que me confirmes el **nombre del canal** y cómo viene el dato del evento (por ejemplo: curso, alumno, saldo nuevo, cuánto cambió, moneda y fecha).

2. **Distribución de saldo:** confirmado que vos nos das la **distribución en monedas**; la de **XP** la maneja **Roadmap**, así que eso lo coordinamos con ellos.

3. **"Retención vs desafíos":** quedamos así — si hablamos de **monedas**, es tu tema; si hablamos de **XP**, es de Roadmap. Nosotros juntamos las dos cosas para el reporte.

4. **Nombres de los canales:** cuando **Notificaciones** publique la lista de canales por release, alineamos el nombre del evento con eso.

En resumen: cuando me pases el nombre del evento y cómo viene, queda cerrado. 😄

---

## Adenda — PAR-12 (vidas iniciales/máximo) pasa a Banco

> **Estado: ✅ ACORDADO** — Banco confirmó que usa este payload (el que proponemos). PAR-12 queda **EXTERNO (Banco)**; el Backoffice no lo almacena.

Banco asume la gestión de **PAR-12** y propuso el evento PARAMETER_UPDATED. Propuesta corregida (envelope estándar de plataforma + version):

```json
{
  "eventId": "uuid",
  "eventType": "PARAMETER_UPDATED",
  "occurredAt": "2026-09-17T14:32:00Z",
  "correlationId": "uuid",
  "actorId": "system:bank-service",
  "role": "MS",
  "source": "bank-service",
  "payload": {
    "parameterId": "PAR-12",
    "version": 3,
    "value": {
      "initialLives": 3,
      "maxLives": 3
    }
  }
}
```n
- Topic: ank.events (naming con T11).
- Backoffice **no almacena** PAR-12 (EXTERNO); solo lo consume si necesita vidas para reporting.
