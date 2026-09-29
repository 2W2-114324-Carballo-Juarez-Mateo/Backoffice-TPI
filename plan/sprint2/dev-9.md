# dev-9.md (Gianoli, Bruno) - Tareas Sprint 2

> **Capacidad:** 28.4 h - **Asignado:** 42 h - **Repo:** 2026-P4-BE/tpi-backoffice + 2026-P4-FE/2026-PIV-TPI-FE
> **Flujo:** feature/tema-12-*|fix/tema-12-* -> develop - PR con >= 1 aprobacion - sin push directo
> **Division pareja:** los 8 que programan cubren las 5 capas - nadie testea/revisa lo suyo.

- **[BACK]** T08-1 - Ingesta T08 por REST (`/api/bank/**`): verificar/completar el adapter de replay + evento de saldo - 2h
- **[BACK]** #3539 - Fachada de corridas de calibracion (crear, listar, detalle; `maeFinal`, `maxIndividualError` y veredicto de T07) (HU06) - 5h
- **[BACK]** 07-T2 - Endpoint del estado de calibracion del modelo activo (ultimo veredicto y marca de deriva, leidos de T07) (HU07) - 4h
- **[BACK]** 15-T2 - Motor de query dinamico (filtros/periodo/columnas/agrupacion) sobre read models con RLS por `course_id` (US-15) - 12h
- **[FRONT]** #3540 - Pantalla de corridas de calibracion (parte 13) (HU06) - 5h
- **[DOC]** #286 - Redefinida: T07 calcula el MAE y el veredicto; el Backoffice gobierna PAR-14; la deriva la emite T07 (HU07) - 2h
- **[BACK]** #321 - `GET ${app.api.private-path}/reports/platform`, solo ADMIN, sin ranking (HU13) - 4h
- **[FRONT]** #3331 - Solo lectura de parametros para PROFESSOR (depende del permiso de lectura de T01) (HT05) - 3h
- **[TEST]** 08-T3 - Tests de integracion de ingesta: nuevo, duplicado, malformado -> DLT, flag apagado (HU08) - 3h
- **[REV]** 04-T5 - Peer review de seguridad de credenciales y del cliente T07 (HU04) - 2h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).