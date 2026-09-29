# dev-2.md (Carballo Juarez, Mateo) - Tareas Sprint 2

> **Capacidad:** 41.5 h - **Asignado:** 42 h - **Repo:** 2026-P4-BE/tpi-backoffice + 2026-P4-FE/2026-PIV-TPI-FE
> **Flujo:** feature/tema-12-*|fix/tema-12-* -> develop - PR con >= 1 aprobacion - sin push directo
> **Division pareja:** los 8 que programan cubren las 5 capas - nadie testea/revisa lo suyo.

- **[BACK]** 05-T1 - Cliente real de modelos evaluadores (listar, activo, desplegar, activar, borrar) sobre 04-T1 (HU05) - 5h
- **[BACK]** #319 - Agregacion de indicadores: aprobacion, abandono, actividad semanal; CSAT solo si hay datos (HU13) - 6h
- **[BACK]** #330 - Evaluador periodico de metricas contra umbrales + `ThresholdBreached` (HU14) - 6h
- **[BACK]** 15-T4 - `POST /api/backoffice/reports/run` (con `templateId` o `config`) + invariantes: matricula T02, anti-comparacion (RF-RPT-07), anonimato, frescura <= 15 min (US-15) - 8h
- **[FRONT]** #276 - Modal de conmutacion con advertencia (modelo actual -> nuevo, confirmacion explicita, UI en espanol) (HU05) - 3h
- **[FRONT]** 05-T3 - Conectar la pantalla 10 al backend (quitar los datos en memoria) (HU05) - 3h
- **[TEST]** 05-N3 - Specs de guards y de la vista de solo lectura (ADMIN, GESTOR y PROFESSOR) (HT05) - 3h
- **[REV]** #317 - Peer review de seguridad RLS (HU12, critico) - 2h
- **[REV]** 07-T6 - Peer review de HU07 (PAR-14/veredicto/deriva sobre T07) - 1h
- **[DOC]** C2 - Confirmar con T11 los topics de auditoria y notificaciones y el `eventType` de `StudentAtHighRisk` (HT01) - 2h
- **[DOC]** #307 - Reglas de riesgo y esquema del read model (HU11, regla `uh/US-11.md`) - 3h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).