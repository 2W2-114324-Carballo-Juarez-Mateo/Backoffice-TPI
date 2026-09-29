# dev-7.md (Cerquatti, Maximo) - Tareas Sprint 2

> **Capacidad:** 46.4 h - **Asignado:** 50 h - **Repo:** 2026-P4-BE/tpi-backoffice + 2026-P4-FE/2026-PIV-TPI-FE
> **Flujo:** feature/tema-12-*|fix/tema-12-* -> develop - PR con >= 1 aprobacion - sin push directo
> **Division pareja:** los 8 que programan cubren las 5 capas - nadie testea/revisa lo suyo.

- **[TEST]** 06-T5 - Tests de integracion con WireMock de la fachada de calibracion (HU06) - 4h
- **[TEST]** 07-T4 - Tests del estado de calibracion y de la validacion de PAR-14 (HU07) - 3h
- **[TEST]** 14-T4 - Tests: evaluacion en el limite y 1 punto abajo + no-ADMIN 403 (HU14) - 4h
- **[BACK]** 04-T1 - Infraestructura del cliente HTTP hacia T07 (`RestClient` administrado, auth segun C1, `problem+json` -> excepciones, URL base tipada; stub como fallback con flag) (HU04) - 6h
- **[BACK]** 06B-T3 - Verificar `GATEWAY_SHARED_SECRET` (`GatewayTrustProperties`) con el mecanismo que acuerde T01 (HT06) - 3h
- **[BACK]** 06B-T4 - Configurar el topic de auditoria confirmado (C2) y la auditoria delegada en T01 (pantalla 11 sin 502) (HT06) - 2h
- **[BACK]** #311 - RLS por `course_id` (`app.current_course`; `ALL` solo para ADMIN) + regla anti-comparacion, migracion `V19` (HU12) - 6h
- **[FRONT]** #3512 - Guards: pushear `ecae5fd`, abrir la PR y avisar a los duenos de las partes 03, 04, 06, 10 y 14 (HT05) - 2h
- **[TEST]** 06B-T2 - Test de integracion del orden del outbox: falla v1 y v2 no sale antes (HT06, Testcontainers Kafka) - 4h
- **[TEST]** #323 - Tests de anonimato (4, 5 y 6 respuestas) y 403 para quien no es ADMIN (HU13) - 4h
- **[TEST]** 15-T5 - Tests del motor dinamico: RLS (PROFESOR A -> cohorte A 200, cohorte B 403), anti-comparacion, anonimato (US-15) - 8h
- **[REV]** #308 - Peer review del modelado analitico (HU11) - 2h
- **[DOC]** C1 - Confirmar con T07 y T01 la llamada a `/api/llm/admin/*`: ruta, Gateway o Eureka, token, scopes (HT01) - 2h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).