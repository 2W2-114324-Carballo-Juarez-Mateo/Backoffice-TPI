# Solicitud de Contrato — T07 · Evaluación LLM (límites y uso)

> **Emisor:** Backoffice (Tema 12) · **Destinatario:** `llm-service` (Tema 07)
> **Origen:** recibimos su documento `CONTRATOS_T07_LLM_LIMITES.md` ("Explicación de Límites y Uso").
> **Estado:** 🟡 EN CURSO — doc recibido y analizado; respondemos con esta solicitud.

---

## 1. Agradecimiento y lectura del documento

Recibimos y entendemos el documento de Límites y Uso (4 capas: por mensaje, por ejercicio, cuota diaria y techo de presupuesto). Alineados con los tres principios rectores (anti-denial-of-wallet, presupuesto cerrado USD 20/mes, cuidado pedagógico). Todo esto es consistente con **PAR-05, PAR-10, PAR-11, PAR-14, PAR-15** que el Backoffice ya registra para consumo de T07.

## 2. Puntos que necesitamos acordar

### 2.1 · Evento de **alerta de presupuesto → Backoffice** (Capa 4, 70%)
Su doc indica que al **70% ($14,00)** llm-service **"manda alerta a Backoffice"**. Necesitamos el contrato:

- **Topic** propuesto: `llm.budget.events` (naming final con T11).
- **Evento** propuesto: `LLMBudgetAlert` con `{porcentaje, umbral (70/90/100), saldo_usd, techo_usd, fecha}`.
- **Envelope estándar (Drive oficial):** **`EventoDTO{eventId, eventType, timestamp, producer, payload}`** (5 campos; `correlationId/actorId/role` → headers de Kafka).
- **Nivel de consumo:** el Backoffice consume para **mostrar el estado del presupuesto LLM en el panel ADMIN** (y, si T11 lo define, derivar una notificación). No administramos la plataforma de pagos.

### 2.2 · ¿Los límites de las 4 capas se **configuran desde Backoffice** o son fijos?
Su doc abre con *"para que sea fácil de entender **y configurar** desde Backoffice"*, pero la tabla marca todo como **"Parámetro Fijo"** (800 tokens, 10 msgs/25k tokens, 40 consultas, 3 desafíos, USD 20/mes). Necesitamos confirmar:

- **(a) Sólo visualización:** el Backoffice muestra/describe los límites (los ADMIN los entienden), y los valores viven **solo en llm-service**. → Sin parámetros nuevos para nosotros.
- **(b) Configurables desde el panel:** el ADMIN edita esos valores desde el Backoffice → serían **parámetros de T07** que **consumimos/almacenamos** como EXTERNOS (como PAR-03/06/07 con Mercado), y propagaríamos por `GlobalConfigurationChanged`.

**Nuestra recomendación:** **(a)**, dado que el doc los define como fijos y su techo financiero es de seguridad no-configurable. Pero lo definen ustedes.

### 2.3 · PAR-22 (límite de desafíos IA) — reconciliar
Nuestro candidato **PAR-22** era "límite **semanal** de desafíos personalizados IA (5)". Su doc define **3 desafíos prácticos por día** (y **40 consultas/día**). Confirmar:
- ¿El límite de desafíos IA es **por día (3)** y no semanal? → ajustamos PAR-22 a `llm_custom_challenges_daily_limit = 3` o lo marcamos **EXTERNO (T07)** si lo gestionan ustedes.
- ¿Mantienen también el **límite semanal** (si no, descartamos la clave semanal)?

### 2.4 · Modelo asignado al **Moderador de Chat**
Su doc lo marca **"⚠️ MODELO A PROBAR — sujeto a cambios"** (Round Robin de clasificadores gratuitos). Confirmamos que **no** lo tomamos como dependencia dura para Sprint 1; si cambia, ajustamos la documentación de integración. Solo lo tomamos en cuenta para la **observabilidad** (no bloqueante).

## 3. Fuera de alcance
- **Límite diario 40 consultas** y **reset a las 00:00**: operativa interna de llm-service; el Backoffice **no** la administra (salvo que elijan (b) en 2.2).
- **Respuestas 400/429/503**: contrato REST/HTTP propio de llm-service; el Backoffice solo las presenta al usuario con mensajes amigables.

## 4. Estados HTTP que verá el front (para diseñar mensajes)
| Código | Significado (su doc) | Mensaje sugerido en el panel |
|---|---|---|
| `400` | Payload/mensaje supera 800 tokens | "Mensaje demasiado largo (máx. 800 tokens)" |
| `429` | Cuota diaria agotada (40/día) | "Llegaste a tu límite diario; se renueva a las 00:00" |
| `503` | Circuit breaker presupuestario (100%) | "El tutor está pausado por presupuesto; avisaremos cuando se restablezca" |

---

> **Respuesta pedida:** 2.1 (topic + payload de `LLMBudgetAlert`), 2.2 (a o b), 2.3 (PAR-22: diario 3 vs semanal 5).
> Registro: `plan/CONTRATOS.md` · `plan/PARAMETROS.md` · doc de ellos en `plan/solicitudes/CONTRATOS_T07_LLM_LIMITES.md`.