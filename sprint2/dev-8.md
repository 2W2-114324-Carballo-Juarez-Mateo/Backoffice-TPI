# dev-8.md (Cerasulo, Regina) - Tareas Sprint 2

> **Capacidad:** 35.3 h - **Asignado:** 38 h - **Repo:** 2026-P4-BE/tpi-backoffice + 2026-P4-FE/2026-PIV-TPI-FE
> **Flujo:** feature/tema-12-*|fix/tema-12-* -> develop - PR con >= 1 aprobacion - sin push directo
> **Division pareja:** los 8 que programan cubren las 5 capas - nadie testea/revisa lo suyo.

- **[TEST]** 10-M2 - Tests del monitor de frescura: 16 min -> stale, dato llega -> marca retirada (US-10 backfill) - 2h
- **[BACK]** #326 - Modelo `alert_thresholds` + CRUD exclusivo ADMIN (HU14) - 4h
- **[REV]** 06-T7 - Peer review de la fachada de calibracion (HU06) - 1h
- **[REV]** 15-T11 - US-15 fase 2: peer review del builder - 2h
- **[BACK]** 04-T2 - Cliente real de proveedores y credenciales (providers, provider-credentials, discover-models, test-model). La key viaja a T07; nunca se loguea ni se devuelve (HU04) - 5h
- **[BACK]** #310 - `GET ${app.api.private-path}/reports/courses/{courseId}/teacher` con puerto de pertenencia docente (adaptador de T02 segun C3; si no hay respuesta, flag) (HU12) - 6h
- **[BACK]** #320 - Anonimato por umbral minimo, en configuracion (verificar PARAMETROS.md; conflicto con PAR-18) (HU13) - 4h
- **[FRONT]** 04-T3 - Conectar la pantalla 09 al backend (quitar los datos en memoria y pasar a `/api/backoffice/llm/...`) (HU04) - 4h
- **[FRONT]** #1657 - Estado de 2FA y sesion (depende de T01) (HT05) - 3h
- **[TEST]** #306 - Particion de equivalencia y valores limite: 10/11 dias, 4/5 dias, 40/60 %, 70 % (HU11) - 5h
- **[DOC]** 04-T6 - OpenAPI de la fachada de proveedores y modelos (HU04) - 2h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).