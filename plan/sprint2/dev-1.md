# dev-1.md (Paz, Luciano) - Tareas Sprint 2

> **Capacidad:** 39.4 h - **Asignado:** 30 h - **Repo:** 2026-P4-BE/tpi-backoffice (mono-modulo) + 2026-P4-FE/2026-PIV-TPI-FE
> **Flujo:** feature/tema-12-*|fix/tema-12-* -> develop - PR con >= 1 aprobacion - sin push directo
> **Division pareja:** los 8 que programan cubren las 5 capas - nadie testea/revisa lo suyo.

- **[BACK]** 06B-T1 - Orden estricto del outbox por `param_key` (HT06, US-02 CA4) - 4h
- **[FRONT]** 05-N1 - Migrar partes 01/02/04/06 de `/api/administration` y `/api/reports` a `/api/backoffice/...` + retirar parche de `proxy.conf.backoffice-gateway.cjs` (HT05) - 4h
- **[BACK]** #312 - Evento `StudentAtHighRisk` por outbox al pasar a ROJO (HU12, topic segun C2) - 4h
- **[TEST]** 04-T4 - Tests WireMock del cliente de proveedores: exito, 404, 409, 503, key enmascarada (HU04) - 5h
- **[TEST]** 05-T4 - Tests WireMock de la activacion: 200, 409, 503 (HU05) - 4h
- **[TEST]** #314 - Tests de RLS: A->A 200, A->B 403, ALL 403, ADMIN 200 (HU12, Testcontainers) - 5h
- **[REV]** 08-T5 - Peer review de los consumidores (HU08) - 1h
- **[REV]** 06B-T7 - Peer review de las PRs abiertas #47, #48 y #50 (HT06) - 2h
- **[DOC]** 06B-T6 - Documentar el orden por key en el contrato del consumidor (HT06) - 1h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).