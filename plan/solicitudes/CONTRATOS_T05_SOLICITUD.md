# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 05 (Desafíos Prácticos)

> **De:** Equipo Backoffice (Tema 12)  
> **Para:** Equipo Desafíos Prácticos (Tema 05)  
> **Propósito:** Definir los contratos de integración bidireccionales con el microservicio de **Desafíos Prácticos (Tema 05)**:  
> 1. **T05 consume y aplica los parámetros globales** del Backoffice (`PAR-04`, `PAR-05`, `PAR-19`, `PAR-20`).  
> 2. **Backoffice consume las entregas y evaluaciones de desafíos prácticos** para consolidar métricas de desempeño, reportes docentes y alertas de alumnos en riesgo.  
> **Cómo usar este documento:** Es una **solicitud formal**; respondan completando las casillas y campos de confirmación.
> **Estándar (T11/cátedra, 2026-09):** envelope **`EventEnvelope<T>{eventId, eventType, eventVersion, timestamp, producer, payload}`** (6 campos, payload tipado), todo en inglés, `producer` = `spring.application.name`, y **los topics se registran con T11** (no se crean por cuenta propia).

---

## 0. Contexto de la Integración

El **Backoffice (Tema 12)** es el microservicio responsable de la **gobernanza global de la plataforma** y la **consolidación de analíticas y reportes docentes**:
* **Administra y versiona los parámetros globales:** Cualquier cambio en porcentajes de variación, penalizaciones o ventanas de entrega se propaga automáticamente hacia los servicios operativos.
* **Consolida indicadores de cohorte:** Ingiere eventos y lecturas de entregas de código para generar dashboards de progreso y rendimiento sin realizar cálculos de ejecución en tiempo real.

---

## 1. Parámetros que T05 consume del Backoffice

El Backoffice provee los siguientes parámetros de configuración que impactan en la lógica de entrega y evaluación práctica:

| PAR | Concepto | Valor de Referencia | Estado | ¿Lo utiliza T05? | Comentarios / Uso en T05 |
|---|---|---|---|---|---|
| **PAR-04** | Variación por calidad y tiempo | $\pm 15\%$ | ✅ Confirmado (PRD) | ☐ SÍ / ☐ NO | Ajuste porcentual de recompensa por calidad de solución |
| **PAR-05** | Bonus / penalidad por uso de IA | $\pm 20\%$ | ✅ Confirmado (PRD) | ☐ SÍ / ☐ NO | Ponderación según nivel de asistencia de IA registrado |
| **PAR-19** | Penalidad por entrega tardía | $30\%$ | 🟡 Candidato | ☐ SÍ / ☐ NO | Descuento aplicado si la entrega es posterior al deadline |
| **PAR-20** | Ventana de gracia para entrega tardía | $48\text{ h}$ | 🟡 Candidato | ☐ SÍ / ☐ NO | Margen temporal máximo permitido tras el vencimiento |

### Mecanismo de Propagación y Consumo:
1. **Emisión de Eventos:** El Backoffice emite el evento `GLOBAL_CONFIGURATION_CHANGED` en el topic `administration.events` (a **registrar con T11**) a través de un **Transactional Outbox**.
   * **Clave de partición Kafka:** `param_key` (ej. `PAR-19`) para garantizar orden estricto de versiones.
2. **Formato de Payload (`EventoDTO`):**
```json
   {
     "eventId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
     "eventType": "GLOBAL_CONFIGURATION_CHANGED",
     "eventVersion": 1,
     "timestamp": "2026-09-20T12:00:00Z",
     "producer": "tema-12-backoffice-service",
     "payload": {
       "key": "PAR-19",
       "name": "late_submission_penalty_pct",
       "value": 30,
       "version": 2,
       "updatedAt": "2026-09-20T12:00:00Z"
     }
   }
   ```
3. **Estrategia en Consumidor:** Se recomienda mantener una **caché local en T05 con TTL de 10 minutos**, invalidada ante la recepción del evento Kafka (garantiza resiliencia si Backoffice no estuviera disponible).

### Confirmaciones solicitadas a T05:
1. ¿Confirman el consumo de **PAR-04**, **PAR-05**, **PAR-19** y **PAR-20**?
2. ¿Requieren algún parámetro adicional de configuración para desafíos prácticos que deba ser incorporado al catálogo global?
3. ¿Confirman la adopción de caché local con fallback al último valor conocido?

---

## 2. Eventos que el Backoffice consume de T05 (Ingesta de Reporting)

Para alimentar los reportes docentes y los indicadores de deserción/riesgo académico, el Backoffice necesita suscribirse a los eventos de actividad práctica.

### Topic propuesto: `practical.challenge.events` *(a registrar formalmente con T11)*

### Eventos de Interés:

#### A. `PracticalChallengeSubmitted` (Entrega realizada por un alumno)
* **Cuándo se emite:** En el momento exacto en que un alumno envía su solución.
* **Payload sugerido:**
  ```json
  {
    "eventId": "uuid-v4",
    "eventType": "PRACTICAL_CHALLENGE_SUBMITTED",
    "eventVersion": 1,
    "timestamp": "2026-09-20T12:30:00Z",
    "producer": "practical-challenges-service",
    "payload": {
      "submissionId": "sub-12345",
      "challengeId": "ch-9876",
      "studentId": "usr-student-01",
      "courseId": "crs-2026-2w2",
      "submittedAt": "2026-09-20T12:30:00Z",
      "deadline": "2026-09-20T10:00:00Z",
      "isLate": true,
      "graceWindowApplied": true
    }
  }
  ```

#### B. `PracticalChallengeEvaluated` (Resultado y calificación de la entrega)
* **Cuándo se emite:** Tras la ejecución de los tests automáticos y/o revisión docente.
* **Payload sugerido:**
  ```json
  {
    "eventId": "uuid-v4",
    "eventType": "PRACTICAL_CHALLENGE_EVALUATED",
    "eventVersion": 1,
    "timestamp": "2026-09-20T12:35:00Z",
    "producer": "practical-challenges-service",
    "payload": {
      "submissionId": "sub-12345",
      "challengeId": "ch-9876",
      "studentId": "usr-student-01",
      "courseId": "crs-2026-2w2",
      "score": 85.0,
      "passed": true,
      "testsPassed": 8,
      "totalTests": 10,
      "penaltyPercentageApplied": 30.0,
      "parametersApplied": {
        "PAR-04_version": 1,
        "PAR-05_version": 1,
        "PAR-19_version": 2
      }
    }
  }
  ```

---

## 3. Endpoints REST de Respaldo y Replay (Lectura)

En caso de desincronización, inicialización de réplicas o caída de mensajería, el Backoffice requiere endpoints de lectura REST para reconstruir sus *read models* (criterio de frescura máxima $\le 15\text{ min}$):

| Endpoint sugerido | Método | Descripción / Qué devuelve |
|---|---|---|
| `/api/practical-challenges/courses/{courseId}/submissions` | GET | Listado paginado de entregas por curso/cohorte |
| `/api/practical-challenges/courses/{courseId}/summary` | GET | Resumen consolidado: total entregas, aprobados, tasa de éxito y entregas tardías |

### Confirmación solicitada a T05:
- ¿Exponen o tienen planificados estos endpoints bajo el prefijo `/api/practical-challenges/**`?

---

## 4. Formalización y Registro del Acuerdo

| Rol / Responsabilidad | Responsable Tema 12 (Backoffice) | Responsable Tema 05 (Desafíos Prácticos) |
|---|---|---|
| **Nombre y Apellido** | Damian Gabriel Baigorria (Dev 3) | ___________________________ |
| **Fecha de Revisión** | 20/09/2026 | _____ / _____ / 2026 |
| **Estado del Acuerdo** | 🟡 SOLICITUD LISTA | ☐ APROBADO / ☐ CON CAMBIOS |
| **Versión de Contrato**| v1.0.0 | v_____ |

### Observaciones y notas de coordinación:
*(Espacio reservado para comentarios del equipo de Tema 05)*
