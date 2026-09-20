# Registro de Parámetros Globales (PAR) — Backoffice (Tema 12)

> **Registro único** de los parámetros de configuración global del Backoffice. El registro es **genérico y extensible** (`key` + `value` jsonb + `version`), versionado y con cambios **solo hacia adelante** (RF-CFG-06). La modificación es **exclusiva de ADMIN** (RF-CFG-05) y se propaga por evento (**`EventoDTO` de 5 campos del PDF de T11**, `producer = backoffice-service`; nombre de evento y topic de emisión a **registrar con T11** en G1/G4) vía Outbox + caché TTL 10 min en consumidores.
> Los consumidores de la economía: **T03 (Desafíos)** deriva los montos de XP (PAR-01) y emite el monto ya resuelto; **T05/T10** según su dominio. **Banco no consume PAR** (solo registra montos ya resueltos) — confirmado con T08. **PAR-03, PAR-06 y PAR-07 quedan fuera del Backoffice**: los gestiona **T09 (Mercado)** o quien defina la cátedra/Hernán → se marcan **EXTERNOS** y el seed del Backoffice **no** los crea.

## PAR-01..PAR-18 — Confirmados (tabla del PRD, RF-CFG-04)

> La tabla del PRD define **hasta PAR-18** (economía). Las claves técnicas son **sugeridas** (a fijar en LL); los ejemplos oficiales conocidos: PAR-01 = XP por dificultad, PAR-06 = precio de una vida, PAR-14 = tolerancia de calibración, PAR-16 = retención académica, PAR-18 = mínimo de respuestas de encuesta.

| PAR | Concepto | Valor de referencia | Clave sugerida | Consume |
|---|---|---|---|---|
| **PAR-01** | XP base por dificultad | 100 / 250 / 500 | `xp_base_dificultad` | 03 / 10 |
| **PAR-02** | XP de desafíos personalizados | 10 / 20 / 30 | `xp_desafios_personalizados` | 03 / 07 |
| **PAR-03** | Monedas por desafío obligatorio/opcional | 100 / 50 | `monedas_desafio` | 🔵 EXTERNO (T09) |
| **PAR-04** | Variación por calidad/tiempo | ±15% | `variacion_calidad_tiempo_pct` | 03 / 05 |
| **PAR-05** | Bonus/penalidad por uso de IA | ±20% | `bonus_penalidad_ia_pct` | 03 / 05 / 07 |
| **PAR-06** | Precio de una vida | 300 | `precio_vida` | 🔵 EXTERNO (T09) |
| **PAR-07** | Precio de equipamiento | 500 | `precio_equipamiento` | 🔵 EXTERNO (T09) |
| **PAR-08** | XP por defecto para desbloqueo | 500 | `xp_desbloqueo_default` | 10 |
| **PAR-09** | Curva de niveles | definida por PAR-09 | `curva_niveles` | 10 |
| **PAR-10** | Muestreo de auditoría de IA | 10% | `muestreo_auditoria_ia_pct` | 07 |
| **PAR-11** | Umbral anti-fuga | 70% | `umbral_antifuga_pct` | 07 |
| **PAR-12** | Vidas iniciales / máximo | 3 / 3 | `vidas_iniciales_max` | 🔵 EXTERNO (Banco) |
| **PAR-13** | Máximo de reintentos | 3 | `reintentos_max` | 03 |
| **PAR-14** | Tolerancia de calibración | ±5 prom. / ±10 por dimensión | `tolerancia_calibracion` | 07 |
| **PAR-15** | Recalibración | mensual / ante cambio de modelo | `recalibracion_periodo` | 07 |
| **PAR-16** | Retención académica | 5 años | `retencion_anios` | T01 (política) |
| **PAR-17** | Preaviso de retención | 90 días | `preaviso_retencion_dias` | T01 (política) |
| **PAR-18** | Mínimo de respuestas para encuesta | 5 | `min_respuestas_encuesta` | 04 |

> **Nota de coordinación:** PAR-16/17 (retención) y PAR-18 (encuestas) los **aplican** otros dueños (T01 la política de retención; T04 la encuesta). El valor vive en el registro del Backoffice; confirmar con cada tema que lo consultan del registro (no lo hardcodean).

## PAR-19..PAR-23 — Candidatos deducidos (a validar con la cátedra)

> **NO son oficiales**: se dedujeron de la especificación (magic numbers de los temas consumidores + Lámina 6). Valores marcados como *propuesto* no tienen número oficial.

| PAR | Concepto | Valor de referencia | Clave (sugerida) | Consume | Estado |
|---|---|---|---|---|---|
| **PAR-19** | Penalidad por entrega tardía | 30% | `late_submission_penalty_pct` | 05 | CANDIDATO |
| **PAR-20** | Ventana de gracia para entrega tardía | 48 h | `late_submission_window_hours` | 05 · T03 en suspenso | CANDIDATO |
| **PAR-21** | Techo del multiplicador de eventos/rachas | 3x | `event_multiplier_cap` | 10 | SUSPENDIDO |
| **PAR-22** | Límite de desafíos personalizados IA | 🔄 **a reconciliar** (T07 define **3/día**; era propuesto 5/semana) | `llm_custom_challenges_daily_limit` | 07 | CANDIDATO / EXTERNO T07 |
| **PAR-23** | Frescura máxima de lectura en analítica | 15 min | eporting_cache_freshness_minutes | 12 (Reporting) | CANDIDATO |
| **PAR-25** | Plazo máximo de corrección / vencimiento de intento | *propuesto* 7 días | max_correccion_intento_dias | 03 | CANDIDATO |

> **PAR-21 (SUSPENDIDO):** no aparece en la tabla del PRD (la sección 4.1 llega a PAR-18) y el mecanismo de **rachas/misiones** está "para más adelante" en T08/T10 (confirmado en la negociación). Se mantiene **documentado como candidato** dependiente de que T10 defina el mecanismo; no asumir consumo por ningún servicio hasta formalizarlo con la cátedra.

> **Límites del tutor LLM (doc de T07, `solicitudes/CONTRATOS_T07_LLM_LIMITES.md`):** T07 los define como **"Parámetro Fijo"** (800 tokens/msg · 10 msgs o 25k tokens/ejercicio · 40 consultas/día · 3 desafíos/día · techo USD 20/mes con umbrales 70/90/100%). **Pendiente de aclarar** si el Backoffice los **solo visualiza** (recomendado) o los **configura** (→ serían EXTERNOS T07). El **PAR-22** (desafíos IA) se **reconcilia** con este doc: **3/día** vs el candidato semanal de 5. La **alerta de presupuesto al 70% → Backoffice** es un **evento** (`LLMBudgetAlert`, a contratar con T07), no un PAR.

## PAR-24 — Asignado al Tema 01 (fuera del Backoffice)

| PAR | Concepto | Dueño | Estado |
|---|---|---|---|
| **PAR-24** | `session_inactivity_timeout_minutes` (30 min propuesto) | **T01 (Usuarios)** | EXTERNO — resuelto en la negociación de contratos |

## Resumen de estados

| Estado | Significado | PAR |
|---|---|---|
| ✅ CONFIRMADO | Definido por el PRD (sin PAR-03/06/07/12) | PAR-01, PAR-02, PAR-04, PAR-05, PAR-08..11, PAR-13..18 |
| 🟡 CANDIDATO | Deducido, a validar con la cátedra | PAR-19..20, PAR-22, PAR-23, PAR-25 |
| ⚪ SUSPENDIDO | Candidato sin respaldo en el PRD, depende de otro dominio | PAR-21 |
| 🔵 EXTERNO | No es del Backoffice | PAR-03, PAR-06, PAR-07 (T09/Mercado) · PAR-12 (Banco) · PAR-24 (T01) · (PAR-22 → posible EXTERNO T07 según respuesta) |

> **PAR-03/06/07 (EXTERNOS · T09/Mercado):** monedas y precios de catálogo los gestiona **T09 (Mercado)** (confirmado como descartados del Backoffice). **T03** necesita que **Mercado exponga PAR-03** (evento + REST + versión) para el monto de monedas del hecho único — contrato **T03 ↔ Mercado**, no del Backoffice. El seed del Backoffice **no** los incluye. **PAR-12 (EXTERNO · Banco):** vidas iniciales/máximo las gestiona **Banco** (evento `PARAMETER_UPDATED` con envelope estándar + `version`); el seed no lo incluye. **PAR-25 (CANDIDATO):** plazo máximo de corrección/vencimiento de intento, propuesto por T03.

> Detalle de la justificación de los candidatos: `sprint0/PAR-19-23-justificacion.md`.
> Modelo de datos: `GlobalParameter` (`param_key` único, `value` jsonb, `version`, `updated_by/at`) — `sdd/backend/docs/04-modelo-datos.md`.