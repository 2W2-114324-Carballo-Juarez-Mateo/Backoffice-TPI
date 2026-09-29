# dev-2.md (Carballo Juarez, Mateo) — Tareas del Sprint 2

> **Capacidad:** 41,5 h · **Asignado:** 32 h (77 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> **Pendiente del Sprint 1:** tus PRs del backend #47 (topics de T11 v3), #48 y #50 están abiertas; las revisa Luciano (06B-T7).
> Incluye tareas que eran de Julieta: #319 y #307.

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 1 | C2 | DOC | Confirmar con T11 los topics de auditoría (`identity.audit` o `identity.audit.events`) y de notificaciones (`system.notifications` o `sistema.notificaciones`), y el `eventType` de `StudentAtHighRisk` | 2 | — |
| 2–5 | 05-T1 | BACK | Cliente real de modelos evaluadores sobre `LlmAdminClient` (listar, activo, desplegar, activar, borrar) usando la infraestructura de 04-T1 | 5 | 04-T1 (Máximo), C1 |
| 3–4 | 05-N3 | TEST | Specs de guards y de la vista de solo lectura (ADMIN, GESTOR y PROFESSOR) | 3 | #3512, #3331, 05-N2 |
| 4–6 | #276 | FRONT | Modal de conmutación con advertencia: modelo actual → nuevo, confirmación explícita, textos de UI en español ("Activar") | 3 | — |
| 5–6 | 05-T3 | FRONT | Conectar la pantalla 10 (modelos) al backend y quitar los datos en memoria | 3 | 05-T1 |
| 4–6 | #307 | DOC | Reglas de riesgo (regla de US-11.md) y esquema del read model en el SDD | 3 | #303, #304 |
| 6 | 07-T6 | REV | Peer review de HU07 (PAR-14 y estado de calibración) | 1 | 07-T1, 07-T2 |
| 5–7 | #319 | BACK | Agregación de indicadores: aprobación, abandono y actividad semanal; CSAT solo si hay datos | 6 | #305 |
| 7–8 | #321 | BACK | `GET ${app.api.private-path}/reports/platform`, solo ADMIN, sin ranking | 4 | #319 |
| 8 | #317 | REV | Peer review de seguridad RLS de HU12 (crítico) | 2 | #310–#312 |

**Revisan tu trabajo:** Luciano testea 05-T1 (05-T4); Valentina, la pantalla 10 (05-T7); Joaquín revisa la fachada de modelos (05-T6); Máximo testea #319 y #321 (#323) y Damián los revisa (#325).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] Tests de integración con Testcontainers donde haya BD o Kafka · 200/403 por rol
- [ ] PR revisada por otra persona · sin secretos · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
