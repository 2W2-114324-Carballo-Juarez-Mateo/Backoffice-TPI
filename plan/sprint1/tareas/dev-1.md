# dev-1.md (Paz, Luciano) - Tareas Sprint 1

> **Capacidad:** 39.4 h - **Asignado:** 29 h - **Repo:** 2026-P4-BE/tpi-backoffice (mono-modulo, 1 datasource + 2 esquemas)
> **Flujo:** feature/*|fix/* -> develop - release/*|hotfix/* -> main - PR con 1 aprobacion - sin push directo
> **Division pareja:** los 9 devs cubren las 5 capas (BACK + FRONT + TEST + REV + DOC) - nadie testea/revisa lo suyo.

- **[BACK]** I0a - Infra: pom + Discovery Client + adopcion del repo (scaffolding, PR #0) - 3h
- **[BACK]** I0b - Infra: docker-compose (PG + Kafka + app) - 2h
- **[BACK]** I0c - Infra: verify a develop + umbral JaCoCo - 1h
- **[BACK]** US-02 T1a - Migracion outbox_message + 1 datasource/2 esquemas (PR #1) - 5h
- **[BACK]** US-02 T1b - Publisher Outbox -> Kafka + clave de particion = clave del parametro - 5h
- **[FRONT]** FE-1a - Capa HTTP: provideHttpClient + interceptor de errores (ErrorApi) - 3h
- **[TEST]** US-02 T5b - Test de integracion del ciclo de consumo (deduplicacion) - 4h
- **[REV]** US-01 T10a - Peer review de DoD/CA de US-01 (trabajo de otro) - 2h
- **[DOC]** US-02 T8 - Documentar envelope, catalogo de topics y contrato del consumidor - 4h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).

