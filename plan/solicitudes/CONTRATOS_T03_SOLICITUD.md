# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 03 (Motor de Desafíos)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Motor de Desafíos (Tema 03)
> **Propósito:** definir los contratos de integración con el **Motor de Desafíos** en las **dos direcciones**: T03 **aplica los parámetros globales** del Backoffice (deriva montos de XP y emite el monto ya resuelto), y el Backoffice **consume** métricas de actividad de desafíos para su reporting.
> **Cómo usar este documento:** es una **solicitud**; respondan marcando opciones y completando tablas.

---

## 0. Contexto

El **Backoffice (Tema 12)** es **consumidor puro** y dueño de los **parámetros globales (PAR)**. Según la Lámina 3 y la negociación con Banco: **T03 (Motor de Desafíos) deriva los montos de XP/monedas** a partir de los parámetros y **emite un "hecho único" con el monto ya resuelto** que otros (ej. Banco) registran. Además, el Backoffice puede **leer métricas de actividad de desafíos** para reportes/engagement.

## 1. Parámetros que T03 consume del Backoffice

T03 aplica la economía derivando montos desde el registro global:

| PAR | Concepto | Valor de referencia | Estado | **¿Lo usás vos?** |
|---|---|---|---|---|
| **PAR-01** | XP base por dificultad | 100 / 250 / 500 | ✅ Confirmado | ☐ SÍ / ☐ NO |
| **PAR-02** | XP de desafíos personalizados | 10 / 20 / 30 | ✅ Confirmado | ☐ SÍ / ☐ NO |
| **PAR-04** | Variación por calidad/tiempo | ±15% | ✅ Confirmado | ☐ SÍ / ☐ NO |
| **PAR-05** | Bonus/penalidad por uso de IA | ±20% | ✅ Confirmado | ☐ SÍ / ☐ NO |
| **PAR-08** | XP por defecto para desbloqueo | 500 | ✅ Confirmado | ☐ SÍ / ☐ NO |
| **PAR-12** | Vidas iniciales / máximo | 3 / 3 | ✅ Confirmado | ☐ SÍ / ☐ NO |
| **PAR-13** | Máximo de reintentos | 3 | ✅ Confirmado | ☐ SÍ / ☐ NO |
| **PAR-20** | Ventana de gracia para entrega tardía | 48 h | 🟡 Candidato | ☐ SÍ / ☐ NO |
| **PAR-22** | Límite semanal de desafíos personalizados IA | *propuesto* 5/sem | 🟡 Candidato | ☐ SÍ / ☐ NO |

> **Nota:** PAR-03/06/07 ya **no son del Backoffice** (los gestiona T09/Mercado).

**Mecanismo:** publicamos `GlobalConfigurationChanged` (`{key, value, version}`) en `administration.events` (Transactional Outbox, idempotencia por `event_id` + versión). **Caché local con TTL 10 min** + invalidación por evento.

**Confirmación que pedimos (por favor, sea preciso):**
1. **Marcá con exactitud qué parámetros usa T03** (tabla de arriba, columna "¿Lo usás vos?"). Para cada PAR que marques SÍ, confirmá también **cómo lo usás** (¿para derivar el monto del hecho único?).
2. **Listá cualquier PAR-01..23 que usen y no figure** en la tabla (incluidos los que hoy están como candidatos: PAR-19/20/21/22/23).
3. **¿Algún PAR de la lista NO lo usan?** Dilo explícito para no asumir consumo.
4. ¿Usan **caché local con TTL 10 min** + invalidación por evento (respaldo ante caída del Backoffice)?
5. ¿Emite el **monto ya resuelto** (hecho único) que Banco registra — así evitamos que Banco calcule?

## 2. Lectura de métricas de desafíos (opcional, para reporting)

Para los KPIs de **engagement/actividad** del Backoffice nos serviría métricas de desafíos por cohorte:

| Endpoint (propuesta) | Método | Qué devuelve |
|---|---|---|
| `GET /api/challenges/courses/{courseId}/activity` | GET | Actividad de desafíos por cohorte (entregas, ritmo) |
| `GET /api/challenges/courses/{courseId}/summary` | GET | Resumen de desafíos (publicados, resueltos, tasa) |

**Confirmación que pedimos:**
- ¿Exponen lecturas de actividad/resumen de desafíos (bajo `/api/challenges/**`)? ¿O prefieren eventos (`challenge.events`)?
- Si no lo exponen, no nos bloquea (es opcional).

## 3. Frescura e idempotencia

- Read models del Backoffice: **frescura ≤ 15 min** (decisión de arquitectura).
- Idempotencia por `event_id` + versión; reconstrucción por replay vía REST.

## 4. Formato de respuesta

```markdown
## Contrato — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **PAR que consume:** ... | **Caché TTL:** ...
- **Hecho único:** ...
- **Endpoints/eventos:** ...
- **Notas:** ...
```

¡Gracias! Con esto cerramos el consumo de parámetros del Motor de Desafíos y (opcional) las métricas de actividad.