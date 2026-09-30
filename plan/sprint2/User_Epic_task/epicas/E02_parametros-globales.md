# [G06] — PARÁMETROS GLOBALES

> Épica #2 de Taiga · Sprint 2 (28/09 → 11/10/2026)

## Objetivo
Que el ADMIN administre en un solo lugar los parámetros globales de la plataforma (PAR-01 a PAR-24), con versionado, historial y propagación confiable a los temas que los aplican, y que el equipo cierre lo pendiente del Sprint 1 sobre una base ordenada y documentada.

## Suposiciones y Restricciones

### Suposiciones
- Los temas T03, T05, T08 y T10 leen sus parámetros del registro del Backoffice y no los tienen fijos en el código.
- El Gateway autentica y el Backoffice autoriza por rol; los servicios leen con el permiso `backoffice.parameters.read`.
- Los cambios de un parámetro rigen hacia adelante y nunca recalculan datos históricos.

### Restricciones (legales/técnicas)
- Solo el ADMIN modifica parámetros; el PROFESOR puede leerlos.
- Todo cambio se publica por outbox en la misma transacción, con la clave del parámetro como clave de partición (orden estricto por versión).
- Los parámetros externos al Backoffice no se siembran en el registro; PAR-12 (vidas) sí es del Backoffice (decisión del 29/09).
- Sin secretos en el código; cero hard delete de datos.

## Criterios de Aceptación a nivel Épico
- [ ] El ADMIN modifica un parámetro y queda versión, historial y evento publicado en el orden correcto.
- [ ] El registro PAR-01..24 está completo y validado (incluye PAR-12 y la forma de PAR-14).
- [ ] La release v1.0.0 está en `main` y no quedan ramas ni PRs del Sprint 1 sin resolver.
- [ ] La wiki de G06 documenta parámetros, administración y gobernanza con los diagramas de la cátedra.

## Dependencias / Impactos
- Servicios / APIs: backoffice-service, API Gateway (T01), Kafka (T11).
- Módulos afectados: registro de parámetros, outbox, publisher, guards y conexión del frontend.
- Otros equipos: T01 (Gateway y auditoría), T11 (topics), T03, T05, T08 y T10 (consumen parámetros).
- Impacto en datos / migraciones: V18 (topics, release), V24 (PAR-12 y alineación con Skill Hub).
- Feature toggles / flags: no.

## Historias de usuario del Sprint 2

| Historia | Título | Acción en Taiga | Puntos | Prioridad | Tareas |
|---|---|---|---:|---|---:|
| [HU01](../historias/HU01_parametros-globales.md) (#17) | Modificación y versionado de parámetros globales | Mover al Sprint 2 (en "In progress") | 5 | Must | 5 |
| [HU02](../historias/HU02_propagacion-cambio-parametro.md) (#21) | Propagación del cambio de parámetro (Outbox + caché con TTL) | Reabrir y mover al Sprint 2 | 5 | Must | 3 |
| [HT02](../historias/HT02_infra-release-gateway.md) (#1630) | Infra: adopción del scaffolding de cátedra, compose y CI | Reabrir y mover al Sprint 2 | 3 | Must | 5 |
| [HT03](../historias/HT03_documentacion-wiki.md) (#1631) | Documentación de diseño: wiki y diagramas | Reabrir, mover al Sprint 2 y renombrar a "Documentación de diseño: wiki y diagramas" | 3 | Must | 6 |
| [HT04](../historias/HT04_frontend-conexion-backend.md) (#1632) | Frontend: conexión al backend (capa HTTP, guards y feedback) | Mover al Sprint 2 | 5 | Must | 6 |
