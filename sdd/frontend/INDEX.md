# Frontend — Índice de redirección

> **LEELO PRIMERO.** Este mapa te dice qué archivo leer según la tarea. No leas todo.

## Contexto rápido

- **Postura:** **Caso A** — apps Angular SSR independientes por dominio en multirepos, integradas por **Nginx** bajo un mismo dominio.
- **Estado:** **plan definido** (alineado a la consigna de Front — Arquitectura y Despliegue U1). Para el BackOffice: Nginx (web + reverse proxy + deep-links) + **BFF por experiencia** + build Docker 2 etapas + `envsubst` + despliegue Rolling/Feature Flags.
- **Fuentes:** `frontend_plan_comunicacion.md` (plan) · `frontend_arquitectura_analisis.md` (investigación).

## Redirección por tarea

| Tarea | Leer |
|---|---|
| Contexto general y decisiones base | `docs/01-overview.md` |
| Entender BFF (cómo llegan los datos a la app) | `docs/02-bff.md` |
| Comunicación entre apps (eventos/storage) | `docs/03-comunicacion.md` |
| Sesión, login, logout, cookies | `docs/04-sesion.md` |
| UI compartida / consistencia visual | `docs/05-ui.md` |
| Nginx, deep-links, recarga de página | `docs/06-nginx-deeplinks.md` |
| Peticiones duplicadas y 429 (single-flight) | `docs/07-single-flight-429.md` |
| Marketplace de plugins TUP + agentes IA | `docs/08-marketplace.md` |
| **Arquitectura y despliegue (Nginx, Docker 2 etapas, load balancing, estrategia)** | `docs/09-despliegue.md` |
| Restricciones que NUNCA violar | `rules/RULES-*.md` (leer siempre) |
| Tareas de front (BFF, app, pantallas, despliegue) | `tareas/F1..F5.md` |

## Orden recomendado para un agente que arranca

1. `docs/01-overview.md`
2. `rules/RULES-sesion.md` y `rules/RULES-stack.md` (no romper nada)
3. El `docs/` + `skills/` específico de tu tarea.

## Convenciones

- Los `docs/*` = diseño. Los `rules/*` = obligaciones/prohibiciones (prioridad). Los `skills/*` = procedimientos.
- Si un `docs/` contradice una rule, manda la rule y reportá la contradicción.
- Mantené este índice y los archivos sincronizados con los cambios del plan.