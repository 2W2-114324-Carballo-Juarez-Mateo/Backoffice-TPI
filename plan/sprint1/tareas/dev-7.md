# dev-7.md (Cerquatti, Maximo) - Tareas Sprint 1

> **Capacidad:** 46.4 h - **Asignado:** 26 h - **Repo:** 2026-P4-BE/tpi-backoffice (mono-modulo, 1 datasource + 2 esquemas)
> **Flujo:** feature/*|fix/* -> develop - release/*|hotfix/* -> main - PR con 1 aprobacion - sin push directo
> **Division pareja:** los 9 devs cubren las 5 capas (BACK + FRONT + TEST + REV + DOC) - nadie testea/revisa lo suyo.

- **[BACK]** US-03 T1 - Filtro de seguridad e inspeccion de headers del Gateway - 4h
- **[BACK]** US-03 T2 - Cliente HTTP hacia T01 via Gateway (auditoria delegada) - 6h
- **[BACK]** US-03 T10 - Gestion de rol via T01: asignar/revocar, auto-revocacion 400, ultimo admin 409, aviso admins (CA1-4) - 4h
- **[FRONT]** FE-1b - Capa HTTP: interceptor de headers (Idempotency-Key, correlacion) + environment - 3h
- **[TEST]** US-02 T5a - Test de integracion del ciclo Outbox -> publicacion - 4h
- **[REV]** US-08 T7a - Peer review de consumidores: idempotencia (trabajo de otro) - 2h
- **[DOC]** G3 - Solicitud de contrato a T07: proveedor de modelo, deriva/calibracion, PAR-22 - 3h

> **DoD Nivel 0:** tarea terminada - tests verdes - PR con review - sdd/docs actualizados. **Nivel 1:** historia testeada, cobertura 90%, sin deuda, documentada (RLS solo donde aplica).

