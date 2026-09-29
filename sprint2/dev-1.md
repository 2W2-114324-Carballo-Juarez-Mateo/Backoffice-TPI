# dev-1.md (Paz, Luciano) — Tareas del Sprint 2

> **Capacidad:** 39,4 h · **Asignado:** 30 h (76 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Rol extra:** integra `feature/tema-12-backoffice` → `develop` en el frontend (Días 5 y 10).
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> Incluye tareas que eran de Julieta: 04-T4, #312 y 06B-T7.

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 1–2 | 06B-T7 | REV | Peer review de las PRs abiertas del backend #47 (topics de T11 v3), #48 y #50 (de Mateo) | 2 | — |
| 1–3 | 06B-T1 | BACK | Orden estricto por `param_key` en `OutboxMessageRepository.findReadyToPublish`: no tomar una fila si hay una `PENDING` más vieja de la misma key. Rama `fix/tema-12-outbox-key-ordering` | 4 | — |
| 3 | 06B-T6 | DOC | Documentar el orden por key en el contrato del consumidor | 1 | 06B-T1 |
| 2–3 | 05-N1 | FRONT | Migrar las partes 01, 02, 04 y 06 a `/api/backoffice/...` y retirar el parche de `proxy.conf.backoffice-gateway.cjs` | 4 | — |
| 4–5 | 04-T4 | TEST | Tests de integración con WireMock del cliente de proveedores: éxito, 404, 409, 503 y key enmascarada (nunca en logs ni en respuestas) | 5 | 04-T1, 04-T2 |
| 4–5 | 05-T4 | TEST | Tests con WireMock de la activación de modelos: 200, 409 (no aprobado) y 503 (T07 caído) | 4 | 05-T1 (Mateo) |
| 5–7 | #312 | BACK | `StudentAtHighRisk` por outbox al pasar a ROJO, en la misma transacción (topic según C2) | 4 | #304, C2 |
| 7 | 08-T5 | REV | Peer review de los consumidores de T07 y T05 (idempotencia, DLT, flags) | 1 | 08-T1 (Valentina) |
| 8–9 | #314 | TEST | Tests de RLS: PROFESSOR A → A 200, A → B 403, `ALL` 403, ADMIN 200 (Testcontainers con PostgreSQL) | 5 | #310, #311 |

**Revisan tu trabajo:** Máximo testea 06B-T1 (06B-T2) y Valentina lo revisa (06B-T5); Joaquín revisa 05-N1 (05-N4); Damián testea #312 (#315) y Mateo lo revisa (#317).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] Tests de integración con Testcontainers donde haya BD o Kafka · 200/403 por rol · outbox en la misma transacción
- [ ] PR revisada por otra persona · sin secretos · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
