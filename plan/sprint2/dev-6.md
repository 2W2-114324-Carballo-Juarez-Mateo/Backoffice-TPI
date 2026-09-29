# dev-6.md (Maldonado, Valentina) - Tareas Sprint 2

> **Capacidad:** 35.3 h - **Asignado:** 32 h - **Repo:** 2026-P4-BE/tpi-backoffice + 2026-P4-FE/2026-PIV-TPI-FE
> **Flujo:** feature/tema-12-*|fix/tema-12-* -> develop - PR con >= 1 aprobacion - sin push directo
> **Division pareja:** los 8 que programan cubren las 5 capas - nadie testea/revisa lo suyo.

- **[BACK]** B-AL - Alerta de presupuesto LLM (70% -> Backoffice): consumidor de `llm.budget.events` (T07) detras de flag; `LLMBudgetAlert` -> notificacion (revisar CONTRATOS.md T07) - 2h
- **[BACK]** 08-T1 - Consumidores de `llm.events` (T07, filtrar por `eventType` segun contrato) y de T05, detras de flags, con dedup y DLT (HU08) - 5h
- **[BACK]** #305 - Job programado de recalculo desde `ingested_event` (T03 `challenge.events` y T02 `course.events`) (HU11) - 5h
- **[FRONT]** #291 - Rehacer el badge de frescura revertido en la PR #99 (HT05) - 3h
- **[FRONT]** 05-N2 - Dashboard: ocultar los accesos no permitidos a GESTOR y PROFESSOR (HT05) - 2h
- **[FRONT]** #322 - Dashboard de KPIs con aviso de "muestra insuficiente" (HU13) - 5h
- **[TEST]** 05-T7 - Specs de las pantallas 09 y 10 conectadas (HU05) - 3h
- **[REV]** 06B-T5 - Peer review de concurrencia del outbox y del secreto del Gateway (HT06) - 2h
- **[REV]** 15-T7 - Peer review de seguridad del motor de reportes (RLS/whitelist) (US-15) - 3h
- **[DOC]** 08-T4 - Actualizar el mapeo de contratos de lectura con T07 y T05 (HU08) - 2h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).