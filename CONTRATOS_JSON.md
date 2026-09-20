# Contratos JSON y Catálogo de Eventos — Backoffice (Tema 12)

> **Fuente de verdad de contratos de datos JSON y mensajería.**  
> Este documento especifica los esquemas JSON canónicos para todos los eventos asíncronos (Apache Kafka) y endpoints síncronos (REST) que involucran al microservicio Backoffice.

> **⚠️ Reconciliado con el Drive oficial (2026-09):** el envelope es el **`EventoDTO`** de 5 campos con `eventType` en **español** y topics en **español** (`desafios.resultados`, `cursos.ciclo-vida`, `sistema.notificaciones`). Donde el nombre oficial aún no está ratificado (T11 es dueño del Kafka), se marca **[a fijar en G1]**. `correlationId/actorId/role` van en **headers de Kafka**, no en el body.

---

## 🏛️ 0. Estándar de Mensajería de Cátedra (`EventoDTO`)

Todos los eventos de la plataforma comparten el **`EventoDTO` oficial de cátedra** en el cuerpo del mensaje y transportan los metadatos de contexto en los **Headers de Kafka**.

### Estructura del Envelope (`EventoDTO`)
```json
{
  "eventId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "eventType": "CAMBIO_CONFIGURACION_GLOBAL",
  "timestamp": "2026-09-19T14:30:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "...": "..."
  }
}
```
> `eventType` en **español** (SCREAMING_SNAKE_CASE) por estándar Drive. Los nombres de nuestros eventos están **[a fijar en G1]**; en los ejemplos de abajo se usa el nombre provisional heredado.

### Metadatos de Transporte (Headers de Kafka)
| Header | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| `traceparent` | `String` | Trazabilidad distribuida estándar W3C | `00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01` |
| `X-Request-Id` | `String` | Identificador único de solicitud HTTP de origen | `c7b2377a-6bdf-4ef8-a536-22442cf28d1c` |
| `correlationId` | `String` | ID de correlación (trazabilidad) | `68d3c1a2-...` |
| `actorId` | `String` | ID o UUID del usuario o servicio que originó la mutación | `usr-admin-8831` |
| `role` | `String` | Rol del actor en el momento de la mutación | `ADMIN` |

---

## 📤 1. Eventos Emitidos por Backoffice (Productor)

### 1.1 Topic: `administration.events` *(nombre **[a fijar en G1]**, en español según Drive)*
- **Partition Key Obligatoria:** Clave del parámetro (`paramKey`, ej: `"xp_base_dificultad"`).
- **Semántica:** Notifica a la plataforma la mutación de un parámetro global para invalidación de caché y versionado.
- **`eventType` provisional** en los ejemplos: `GLOBAL_CONFIGURATION_CHANGED` → **[a fijar en español, ej. `CAMBIO_CONFIGURACION_GLOBAL`]**. Payload con `eventId`/`timestamp`/`producer`/`payload` (headers: `traceparent`, `X-Request-Id`, `correlationId`, `actorId`, `role`).

#### Ejemplo 1: Parámetro Compuesto de Dificultad (`PAR-01` — `xp_base_dificultad`)
```json
{
  "eventId": "a9b1c2d3-e4f5-4678-90ab-cdef12345678",
  "eventType": "GLOBAL_CONFIGURATION_CHANGED",
  "timestamp": "2026-09-19T14:30:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "paramKey": "xp_base_dificultad",
    "value": {
      "FACIL": 100,
      "MEDIO": 250,
      "DIFICIL": 500
    },
    "version": 2,
    "previousVersion": 1,
    "description": "XP base otorgada según nivel de dificultad del desafío",
    "updatedAt": "2026-09-19T14:30:00Z",
    "updatedBy": "usr-admin-8831"
  }
}
```

#### Ejemplo 2: Parámetro de Porcentaje / Rango (`PAR-05` — `bonus_penalidad_ia_pct`)
```json
{
  "eventId": "b2c3d4e5-f6a7-4890-12bc-def345678901",
  "eventType": "GLOBAL_CONFIGURATION_CHANGED",
  "timestamp": "2026-09-19T14:35:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "paramKey": "bonus_penalidad_ia_pct",
    "value": 20,
    "version": 3,
    "previousVersion": 2,
    "description": "Porcentaje de bonus o penalidad aplicado por el uso del tutor de IA",
    "updatedAt": "2026-09-19T14:35:00Z",
    "updatedBy": "usr-admin-8831"
  }
}
```

#### Ejemplo 3: Parámetro Numérico Entero (`PAR-13` — `reintentos_max`)
```json
{
  "eventId": "c3d4e5f6-a7b8-4901-23cd-ef4567890123",
  "eventType": "GLOBAL_CONFIGURATION_CHANGED",
  "timestamp": "2026-09-19T14:40:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "paramKey": "reintentos_max",
    "value": 3,
    "version": 1,
    "previousVersion": null,
    "description": "Cantidad máxima de reintentos permitidos por desafío",
    "updatedAt": "2026-09-19T14:40:00Z",
    "updatedBy": "usr-admin-8831"
  }
}
```

#### Ejemplo 4: Curva de Niveles (`PAR-09` — `curva_niveles`)
```json
{
  "eventId": "d4e5f6a7-b8c9-4012-34de-f56789012345",
  "eventType": "GLOBAL_CONFIGURATION_CHANGED",
  "timestamp": "2026-09-19T14:45:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "paramKey": "curva_niveles",
    "value": [
      { "nivel": 1, "xpRequerida": 0 },
      { "nivel": 2, "xpRequerida": 500 },
      { "nivel": 3, "xpRequerida": 1200 },
      { "nivel": 4, "xpRequerida": 2200 },
      { "nivel": 5, "xpRequerida": 3500 }
    ],
    "version": 1,
    "previousVersion": null,
    "description": "Curva progresiva de XP acumulada por nivel",
    "updatedAt": "2026-09-19T14:45:00Z",
    "updatedBy": "usr-admin-8831"
  }
}
```

---

### 1.2 Topic: `audit.events` *(a ratificar con T11)*
- **Partition Key:** `actorId` (header).
- **Destino:** Módulo central de auditoría de Tema 01 (Usuarios).
- **`eventType` provisional:** `ParameterChanged` → **[a fijar en español, ej. `PARAMETRO_CAMBIADO`]**. `actorId`/`role` → **headers de Kafka** (no en el payload).

#### Ejemplo: Registro de Mutación de Parámetro (`ParameterChanged`)
```json
{
  "eventId": "e5f6a7b8-c9d0-4123-45ef-678901234567",
  "eventType": "ParameterChanged",
  "timestamp": "2026-09-19T14:30:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "action": "UPDATE",
    "target": "GlobalParameter",
    "targetKey": "xp_base_dificultad",
    "oldValue": { "FACIL": 100, "MEDIO": 200, "DIFICIL": 400 },
    "newValue": { "FACIL": 100, "MEDIO": 250, "DIFICIL": 500 },
    "version": 2,
    "clientIp": "192.168.1.50"
  }
}
```
> `actorId` (`usr-admin-8831`) y `actorRole` (`ADMIN`) viajan en los **headers de Kafka**, no en el body.

---

### 1.3 Topic: `sistema.notificaciones` (Tema 11 — Social y Notificaciones) ✅ Drive
- **Partition Key:** `cohortId`.
- **Destino:** Tema 11 (Social y Notificaciones).
- **Evento confirmado (Drive):** **`VENCIMIENTO_DATOS_ACADEMICOS`** (preaviso de retención, PAR-16/17).
- Los eventos `StudentAtHighRisk` y `DataStaleDetected` **NO están en el Drive** → **[a confirmar con T11]** si se emiten o se descartan (el topic `backoffice.alerts.events` queda pendiente de esa definición).

#### Ejemplo (confirmado): Preaviso de Vencimiento de Datos Académicos (`VENCIMIENTO_DATOS_ACADEMICOS`)
```json
{
  "eventId": "f6a7b8c9-d0e1-4234-56fa-789012345678",
  "eventType": "VENCIMIENTO_DATOS_ACADEMICOS",
  "timestamp": "2026-09-19T15:00:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "cohortId": "COH-2021-P1",
    "courseName": "Programación I - 2021",
    "closingDate": "2021-12-01",
    "expirationDate": "2026-12-01",
    "daysRemaining": 90
  }
}
```

#### Ejemplo (a confirmar con T11): Alumno en Riesgo Académico Crítico (`StudentAtHighRisk`)
```json
{
  "eventId": "f6a7b8c9-d0e1-4234-56fa-789012345678",
  "eventType": "StudentAtHighRisk",
  "timestamp": "2026-09-19T15:00:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "studentId": "alu-9843",
    "courseId": "cur-2026-2w2",
    "riskLevel": "CRITICO_ROJO",
    "inactivityDays": 14,
    "failedAttemptsRate": 0.85,
    "suggestedAction": "Intervención docente recomendada"
  }
}
```

#### Ejemplo (a confirmar con T11): Frescura de Datos Degradada (`DataStaleDetected`)
```json
{
  "eventId": "07b8c9d0-e1f2-4345-67ab-890123456789",
  "eventType": "DataStaleDetected",
  "timestamp": "2026-09-19T15:10:00Z",
  "producer": "tema-12-backoffice",
  "payload": {
    "courseId": "cur-2026-2w2",
    "reportType": "ENGAGEMENT_PANEL",
    "minutesStale": 25,
    "maxFreshnessAllowed": 15,
    "reason": "Retraso en la ingesta de desafios.resultados"
  }
}
```

---

## 🌐 2. Contratos REST de Parámetros (Síncronos)

### 2.1 `GET /api/administration/parameters`
Utilizado por los microservicios en arranque en frío (Cold Start) para poblar su caché local.

**Response `200 OK`:**
```json
[
  {
    "key": "xp_base_dificultad",
    "value": {
      "FACIL": 100,
      "MEDIO": 250,
      "DIFICIL": 500
    },
    "version": 2,
    "updatedAt": "2026-09-19T14:30:00Z"
  },
  {
    "key": "bonus_penalidad_ia_pct",
    "value": 20,
    "version": 3,
    "updatedAt": "2026-09-19T14:35:00Z"
  },
  {
    "key": "reintentos_max",
    "value": 3,
    "version": 1,
    "updatedAt": "2026-09-19T14:40:00Z"
  }
]
```

---

### 2.2 `PUT /api/administration/parameters/{key}`
Modificación exclusiva para usuarios con rol `ADMIN`.

**Headers requeridos:**
- `X-User-Roles: ADMIN`
- `X-User-Id: usr-admin-8831`
- `Idempotency-Key: 7b844f24-4f4c-47bc-8f47-c0bc44e7829a`

**Request Body:**
```json
{
  "value": {
    "FACIL": 100,
    "MEDIO": 250,
    "DIFICIL": 500
  },
  "description": "Ajuste de XP en dificultad media y difícil solicitado por coordinación académica"
}
```

**Response `200 OK`:**
```json
{
  "key": "xp_base_dificultad",
  "value": {
    "FACIL": 100,
    "MEDIO": 250,
    "DIFICIL": 500
  },
  "version": 2,
  "description": "Ajuste de XP en dificultad media y difícil solicitado por coordinación académica",
  "updatedAt": "2026-09-19T14:30:00Z",
  "updatedBy": "usr-admin-8831"
}
```

---

### 2.3 Contrato de Error Oficial de Cátedra (`ErrorApi`)
Formato estricto para respuestas `4xx` y `5xx`.

**Response `400 Bad Request`:**
```json
{
  "timestamp": "2026-09-19T14:30:05.123Z",
  "status": 400,
  "error": "Bad Request",
  "message": "El valor del parámetro 'bonus_penalidad_ia_pct' debe estar comprendido entre 0 y 50%"
}
```

---

## 📥 3. Eventos Consumidos por Backoffice (Consumidor)

### 3.1 Topic: `desafios.resultados` (Tema 03 — Motor de Desafíos) ✅ Drive
- **Hecho Único:** Ingesta de finalización de desafíos con desglose congelado.
- **`eventType`:** `IntentoDesafioFinalizado` → **[a ratificar en español con T03]**. Envelope `EventoDTO` + headers.

#### Ejemplo: `IntentoDesafioFinalizado`
```json
{
  "eventId": "18c9d0e1-f2a3-4456-78bc-901234567890",
  "eventType": "IntentoDesafioFinalizado",
  "timestamp": "2026-09-19T15:20:00Z",
  "producer": "tema-03-desafios",
  "payload": {
    "intentoId": "int-77412",
    "desafioId": "des-prog4-001",
    "studentId": "alu-9843",
    "courseId": "cur-2026-2w2",
    "resultado": "APROBADO",
    "tiempoResolucionSegundos": 420,
    "intentosRealizados": 2,
    "usoTutorIa": true,
    "cantidadConsultasIa": 3,
    "xpGanada": 240,
    "parametrosAplicados": {
      "xpBase": 250,
      "versionXpBase": 2,
      "variacionCalidadTiempo": 0.05,
      "factorIa": -0.10,
      "versionFactorIa": 3
    }
  }
}
```

---

### 3.2 Topic: `cursos.ciclo-vida` (Tema 02 — Cursos y Matrícula) ✅ Drive
- **Propósito:** Mantener dimensiones de cohortes y encuestas de satisfacción CSAT.
- **`eventType`:** `RosterUpdated` / `SurveySubmitted` → **[a ratificar en español con T02]**. Envelope `EventoDTO` + headers.

#### Ejemplo 1: Actualización de Matrícula (`RosterUpdated`)
```json
{
  "eventId": "29d0e1f2-a3b4-4567-89cd-012345678901",
  "eventType": "RosterUpdated",
  "timestamp": "2026-09-19T15:25:00Z",
  "producer": "tema-02-cursos",
  "payload": {
    "courseId": "cur-2026-2w2",
    "action": "ENROLL",
    "studentId": "alu-9843",
    "effectiveDate": "2026-09-19T15:25:00Z"
  }
}
```

#### Ejemplo 2: Envío de Encuesta Docente CSAT Anónima (`SurveySubmitted`)
```json
{
  "eventId": "3ae1f2a3-b4c5-4678-90de-123456789012",
  "eventType": "SurveySubmitted",
  "timestamp": "2026-09-19T15:30:00Z",
  "producer": "tema-02-cursos",
  "payload": {
    "courseId": "cur-2026-2w2",
    "profesorId": "prof-5510",
    "satisfaccionGeneral": 4.5,
    "claridadExplicaciones": 5.0,
    "dificultadPercibida": 3.5,
    "comentarioAnonimo": "Excelente ritmo de la cursada",
    "fechaRespuesta": "2026-09-19T15:30:00Z"
  }
}
```

---

### 3.3 Topic: `identity.events` (Tema 01 — Usuarios) *(a ratificar con T11/T01)*
- **Propósito:** Anonimización de datos personales por derecho al olvido o expiración de retención (`PAR-16`).
- **`eventType`:** `DataAnonymized` → **[a ratificar en español con T01]**. Envelope `EventoDTO` + headers.

#### Ejemplo: `DataAnonymized`
```json
{
  "eventId": "4bf2a3b4-c5d6-4789-01ef-234567890123",
  "eventType": "DataAnonymized",
  "timestamp": "2026-09-19T15:35:00Z",
  "producer": "tema-01-usuarios",
  "payload": {
    "studentId": "alu-9843",
    "anonymizedAt": "2026-09-19T15:35:00Z",
    "policyApplied": "RETENTION_POLICY_EXPIRED"
  }
}
```
