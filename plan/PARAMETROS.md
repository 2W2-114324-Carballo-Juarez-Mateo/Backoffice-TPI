# Registro de Parámetros Globales (PAR) — Backoffice (Tema 12)

> **Registro único** de los parámetros de configuración global del Backoffice. El registro es **genérico y extensible** (`key` + `value` jsonb + `version`), versionado y con cambios **solo hacia adelante** (RF-CFG-06). La modificación es **exclusiva de ADMIN** (RF-CFG-05) y se propaga por evento (**`EventEnvelope<T>` de 6 campos** de T11/cátedra, `producer = tema-12-backoffice-service`) vía Outbox + caché TTL 10 min en consumidores.
> **Dueño de TODOS los PAR-01..24: el Backoffice** (contratos publicados en Skill Hub, `backoffice-global-parameters-contract` v1). Los demás temas son **consumidores**: T03 deriva los montos de XP (PAR-01) y emite el monto ya resuelto; T08/T09/T01 **leen** PAR-12/PAR-03-06-07/PAR-24 de nosotros. **No hay PAR "externos"** (antes se marcaron EXTERNOS PAR-03/06/07 de T09, PAR-12 de T08 y PAR-24 de T01 por error): el seed del Backoffice ya los incluye (**V24**, mergeada).

## PAR-01..PAR-18 — Confirmados (tabla del PRD, RF-CFG-04)

> La tabla del PRD define **hasta PAR-18** (economía). Las claves técnicas son **sugeridas** (a fijar en LL); los ejemplos oficiales conocidos: PAR-01 = XP por dificultad, PAR-06 = precio de una vida, PAR-14 = tolerancia de calibración, PAR-16 = retención académica, PAR-18 = mínimo de respuestas de encuesta.

| PAR | Concepto | Valor de referencia | Clave sugerida | Consume |
|---|---|---|---|---|
| **PAR-01** | XP base por dificultad | 100 / 250 / 500 | `xp_base_dificultad` | 03 / 10 |
| **PAR-02** | XP de desafíos personalizados | 10 / 20 / 30 | `xp_desafios_personalizados` | 03 / 07 |
| **PAR-03** | Monedas por desafío obligatorio/opcional | 100 / 50 | `monedas_desafio` | 03 / 09 |
| **PAR-04** | Variación por calidad/tiempo | ±15% | `variacion_calidad_tiempo_pct` | 03 / 05 |
| **PAR-05** | Bonus/penalidad por uso de IA | ±20% | `bonus_penalidad_ia_pct` | 03 / 05 / 07 |
| **PAR-06** | Precio de una vida | 300 | `precio_vida` | 09 |
| **PAR-07** | Precio de equipamiento | 500 | `precio_equipamiento` | 09 |
| **PAR-08** | XP por defecto para desbloqueo | 500 | `xp_desbloqueo_default` | 10 |
| **PAR-09** | Curva de niveles | definida por PAR-09 | `curva_niveles` | 10 |
| **PAR-10** | Muestreo de auditoría de IA | 10% | `muestreo_auditoria_ia_pct` | 07 |
| **PAR-11** | Umbral anti-fuga | 70% | `umbral_antifuga_pct` | 07 |
| **PAR-12** | Vidas iniciales / máximo | `{"initialLives": 3, "maxLives": 3}` | `vidas_iniciales_max` | 08 |
| **PAR-13** | Máximo de reintentos | 3 | `reintentos_max` | 03 |
| **PAR-14** | Tolerancia de calibración | `{"average": 5, "dimension": 10}` (Skill Hub) | `tolerancia_calibracion` | 07 |
| **PAR-15** | Recalibración | mensual / ante cambio de modelo | `recalibracion_periodo` | 07 |
| **PAR-16** | Retención académica | 5 años | `retencion_anios` | T01 (política) |
| **PAR-17** | Preaviso de retención | 90 días | `preaviso_retencion_dias` | T01 (política) |
| **PAR-18** | Mínimo de respuestas para encuesta | 5 | `min_respuestas_encuesta` | 04 |

> **Nota de coordinación:** PAR-16/17 (retención) y PAR-18 (encuestas) los **aplican** T01/T04, pero **el valor vive y lo gobierna el Backoffice** (contratos Skill Hub). Confirmar con cada tema que lo consultan del registro (no lo hardcodean).

## PAR-19..PAR-23 — Candidatos deducidos (a validar con la cátedra)

> **NO son oficiales**: se dedujeron de la especificación (magic numbers de los temas consumidores + Lámina 6). Valores marcados como *propuesto* no tienen número oficial.

| PAR | Concepto | Valor de referencia | Clave (sugerida) | Consume | Estado |
|---|---|---|---|---|---|
| **PAR-19** | Penalidad por entrega tardía | 30% | `late_submission_penalty_pct` | 05 | CANDIDATO |
| **PAR-20** | Ventana de gracia para entrega tardía | 48 h | `late_submission_window_hours` | 05 · T03 en suspenso | CANDIDATO |
| **PAR-21** | Techo del multiplicador de eventos/rachas | 3x | `event_multiplier_cap` | 10 | SUSPENDIDO |
| **PAR-22** | Límite de desafíos personalizados IA | `3` (desafíos/día — Skill Hub, `backoffice-t07-evaluacion-llm-contract` v1) | `llm_custom_challenges_daily_limit` | 07 | Confirmado (Skill Hub) |
| **PAR-23** | Frescura máxima de lectura en analítica | 15 min | eporting_cache_freshness_minutes | 12 (Reporting) | CANDIDATO |
| **PAR-25** | Plazo máximo de corrección / vencimiento de intento | *propuesto* 7 días | max_correccion_intento_dias | 03 | CANDIDATO |

> **PAR-21 (SUSPENDIDO):** no aparece en la tabla del PRD (la sección 4.1 llega a PAR-18) y el mecanismo de **rachas/misiones** está "para más adelante" en T08/T10 (confirmado en la negociación). Se mantiene **documentado como candidato** dependiente de que T10 defina el mecanismo; no asumir consumo por ningún servicio hasta formalizarlo con la cátedra.

> **Límites del tutor LLM (doc de T07, `solicitudes/CONTRATOS_T07_LLM_LIMITES.md`):** T07 los define como **"Parámetro Fijo"** (800 tokens/msg · 10 msgs o 25k tokens/ejercicio · 40 consultas/día · 3 desafíos/día · techo USD 20/mes con umbrales 70/90/100%). **Pendiente de aclarar** si el Backoffice los **solo visualiza** (recomendado) o los **configura** (→ serían EXTERNOS T07). El **PAR-22** (desafíos IA) se **reconcilia** con este doc: **3/día** vs el candidato semanal de 5. La **alerta de presupuesto al 70% → Backoffice** es un **evento** (`LLMBudgetAlert`, a contratar con T07), no un PAR.

## PAR-24 — Consumido por T01 (dueño: Backoffice)

| PAR | Concepto | Consume | Estado |
|---|---|---:|---|
| **PAR-24** | `session_inactivity_timeout_minutes` (30 min propuesto) | **T01 (Usuarios)** | Confirmado (Skill Hub) — seed pendiente |

## Resumen de estados

| Estado | Significado | PAR |
|---|---|---|
| ✅ CONFIRMADO | Dueño: **Backoffice** (PRD + contratos Skill Hub) | PAR-01..18 (todos del Backoffice; los demás temas los consumen) |
| 🟡 CANDIDATO | Deducido, a validar con la cátedra | PAR-19..20, PAR-22, PAR-23, PAR-25 |
| ⚪ SUSPENDIDO | Candidato sin respaldo en el PRD, depende de otro dominio | PAR-21 |

> **Consumidores (matriz Skill Hub):** PAR-03/06/07 → T09 · PAR-12 → T08 · PAR-24 → T01 · PAR-16/17 → T01 · PAR-18 → T02 · PAR-01/08/09 → T10 · PAR-04/05/19/20 → T05 · PAR-02/05/10/11/14/15/22 → T07 · PAR-01/02/03/04/05/13 → T03. **Todos los PAR son del Backoffice** (dueño/seed); el seed ya está completo (**V24**: PAR-03/06/07/12/24 sembrados y PAR-14 con clave `average`). **PAR-25 (CANDIDATO):** plazo máximo de corrección/vencimiento de intento, propuesto por T03.

> Detalle de la justificación de los candidatos: `sprint0/PAR-19-23-justificacion.md`.
> Modelo de datos: `GlobalParameter` (`param_key` único, `value` jsonb, `version`, `updated_by/at`) — `sdd/backend/docs/04-modelo-datos.md`.