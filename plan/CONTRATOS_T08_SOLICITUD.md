# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 08 (Banco)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Banco (Tema 08)
> **Propósito:** Definir los contratos de integración con el Tema 08, en las **dos direcciones**: el Backoffice **consume** datos económicos de Banco para sus reportes/métricas, y Banco **consume** parámetros globales del Backoffice.
> **Cómo usar este documento:** es una **solicitud**. Respondan cada contrato completando las tablas y marcando opciones. Al final hay una plantilla de respuesta.

---

## 0. Contexto (una pantalla)

El **Backoffice (Tema 12)** es **consumidor puro**. Para su reporting necesita **leer datos económicos del Tema 08 (Banco)**; y a su vez, el Tema 08 **aplica la economía** configurada en los **parámetros globales del Backoffice** (debe leerlos, no tenerlos hardcodeados — Lámina 6). Por eso necesitamos acordar:

1. **Eventos de Banco** (`bank.events`) — bloquea la actualización de read models.
2. **Contrato REST de lectura** (`/api/bank/**`) — para reconstruir read models.
3. **Parámetros que T08 consume** (PAR-03/06/07/21) + mecanismo de caché.
4. **Frescura ≤ 15 min + idempotencia**.

---

## 1. Eventos de Banco (`bank.events`)

Proponemos consumir los eventos económicos para mantener nuestros read models de reporting al día.

**Eventos que nos interesan (a confirmar el nombre real):**

| Evento propuesto | Qué representa | Datos mínimos sugeridos |
|---|---|---|
| `TransactionCreated` | Movimiento/transacción económica de un alumno | `courseId`, `studentId`, `txId`, `amount`, `currency` (XP/monedas), `type`, `occurredAt` |
| `BalanceChanged` | Cambio de saldo/XP de un alumno | `courseId`, `studentId`, `newBalance`, `delta`, `occurredAt` |
| `MultiplierApplied` (si aplica) | Aplicación del multiplicador de eventos/rachas | `courseId`, `studentId`, `multiplier`, `capApplied`, `occurredAt` |

**Envelope estándar de plataforma:**

```json
{
  "eventId": "uuid",
  "eventType": "TransactionCreated",
  "occurredAt": "2026-09-12T14:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "role": "MS",
  "source": "bank-service",
  "payload": {
    "courseId": "course-10",
    "studentId": "student-42",
    "txId": "tx-7",
    "amount": 300,
    "currency": "MONEDAS",
    "type": "COMPRA_VIDA",
    "occurredAt": "2026-09-12T14:00:00Z"
  }
}
```

> `role` se agrega al envelope como estándar de plataforma (en curso con T01).

**Por favor confirmen:**
- ¿El topic es `bank.events`? ¿Qué eventos publican realmente y con qué payload versionado?
- ¿Los IDs reutilizan los de plataforma (`courseId`/`studentId` UUID)?
- ¿Con qué frecuencia emiten (por transacción, por batch, por día)? (impacta nuestra frescura ≤ 15 min).

---

## 2. Contrato REST de lectura (`/api/bank/**`)

Según la convención de rutas de la plataforma, toda API vive bajo `/api/{servicio}/**`. Asumimos su prefijo `/api/bank/**`. Necesitamos lecturas para **reconstruir read models** (replay) y para métricas que no viajan por evento.

**Endpoints propuestos (a validar):**

| Endpoint (propuesta) | Método | Qué devuelve |
|---|---|---|
| `GET /api/bank/courses/{courseId}/balances` | GET | Saldos/XP por alumno de la cohorte (listado) |
| `GET /api/bank/courses/{courseId}/students/{studentId}/balance` | GET | Saldo/XP de un alumno |
| `GET /api/bank/courses/{courseId}/transactions` | GET | Movimientos de la cohorte (filtrable por rango) |
| `GET /api/bank/courses/{courseId}/distribution` | GET | Distribución de XP/riqueza de la cohorte (agregados) |

**Por favor confirmen:**
- URLs reales, métodos, roles requeridos (nosotros consumimos con token `MS` service-to-service vía gateway).
- Paginación (`offset`/`limit` o cursor), filtros (`courseId`, `dateFrom`, `dateTo`, `type`), formato de respuesta.
- ¿Existe un endpoint de **agregados de retención/consumo** (p. ej. retención vs desafíos) o lo calculamos del stream?

---

## 3. Parámetros que T08 consume del Backoffice

El Tema 08 **aplica la economía** configurada en nuestros parámetros globales:

| PAR | Concepto | Valor de referencia |
|---|---|---|
| **PAR-03** | Monedas por desafío obligatorio/opcional | 100 / 50 |
| **PAR-06** | Precio de una vida | 300 |
| **PAR-07** | Precio de equipamiento | 500 |
| **PAR-21** | Techo del multiplicador de eventos/rachas | 3x |

**Mecanismo:** publicamos `GlobalConfigurationChanged` (`{key, value, version}`) en `administration.events` (Outbox, idempotencia por `event_id` + versión).

**Confirmación que pedimos:**
1. ¿T08 consume efectivamente esos PAR? ¿Hay **algún otro PAR-01..23** que el Banco deba leer?
2. ¿Usan **caché local con TTL** (recomendamos 10 min) + invalidación por evento, como respaldo ante caída del Backoffice?

---

## 4. Frescura e idempotencia

- Nuestros read models se actualizan por eventos; requerimos **frescura ≤ 15 min** (RF-RPT-06) → si emiten batch, la frecuencia debe ser acorde.
- Idempotencia por `event_id` (+ versión): confirmen que cada evento lleva `event_id` único.
- Read models: **reconstruibles por replay** vía el contrato REST del punto 2 (no dependemos del historial del broker).

---

## 5. Lo que el Backoffice aporta (para acelerar)

- Envelope estándar `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}`.
- Idempotencia por `event_id` + versión en todos los consumidores.
- Convención de topics `{dominio}.events` y rutas `/api/{servicio}/**`.
- Nuestros endpoints de reporting que consumirán estos datos (para que definan el acceso).

---

## 6. Formato de respuesta esperado

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

**Prioridad para nosotros:** 1 (eventos `bank.events`) y 2 (lecturas REST) primero; 3 (PAR) a continuación; el resto no bloquea.

¡Gracias! Con estas respuestas cerramos los contratos y desbloqueamos las métricas económicas del Backoffice.