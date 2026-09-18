# dev-7.md (Cerquatti, Maximo) - Tareas Sprint 1

> **Capacidad:** 46.4 h - **Asignado:** 34 h - **Repo:** 2026-P4-BE/tpi-backoffice (mono-modulo, 1 datasource + 2 esquemas)
> **Flujo:** feature/*|fix/* -> develop - release/*|hotfix/* -> main - PR con 1 aprobacion - sin push directo
> **Division pareja:** horas, codigo (BACK+FRONT) y capas balanceados - nadie en una sola capa.

- **[TEST]** US-02 T5 - Test de integracion del ciclo completo Outbox -> Kafka -> consumo - 8h
- **[TEST]** US-03 T5 - Tests de integracion del filtro y autorizacion por headers - 6h
- **[TEST]** FE-6 - Smoke test E2E del flujo de la demo - 4h
- **[BACK]** US-03 T2 - Cliente HTTP hacia T01 via Gateway (auditoria delegada) - 6h
- **[BACK]** US-03 T10 - Gestion de rol via T01: asignar/revocar, auto-revocacion 400, ultimo admin 409, aviso admins (CA1-4) - 4h
- **[BACK]** US-02 T11 - X-Request-Id/traceparent como headers de Kafka - 3h
- **[REV]** US-01 T10 - Peer review y validacion de DoD (US-01) - 3h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).

