# Backend — Índice de redirección

> **LEELO PRIMERO.** Este mapa te dice qué archivo leer según la tarea. No leas todo: buscá tu tarea y entrá al/los archivo(s) indicado(s).

## Contexto rápido

- **Stack:** Java 21 · Spring Boot 3 · Maven (multi-módulo) · PostgreSQL · Kafka · Eureka · Spring Cloud Gateway.
- **Alcance (Tema 12 — Backoffice, consumidor puro):** administración de plataforma (PAR + proveedor LLM exclusiva ADMIN) · reportes docentes · panel del profesor (alumno en riesgo) · métricas CSAT · exportación · alertas · frescura ≤ 15 min · sin comparación entre docentes.
- **Arquitectura:** **2 servicios propietarios** (Administration & Configuration, Reporting & Analytics), Clean Architecture por capas, Database per Service, Outbox + idempotencia.
- **Consume:** identidad/auth/roles/2FA/auditoría → **Tema 01**; cohorte → **Tema 02**. **Lecturas:** 02/04/05/07/08/10 (contratos de lectura).
- **Regla clave:** toda llamada síncrona entre servicios pasa por el **gateway** (no directa).
- **Fuentes:** `backoffice_backend_requerimientos_arquitectura.md` · `TUP_PIV_BE_PROPUESTA_ARQ.pdf` · `BackOffice Resumen Visual.md`.

## Redirección por tarea

| Tarea | Leer |
|---|---|
| Contexto general y alcance | `docs/01-overview.md` |
| Diagrama y principios de la arquitectura | `docs/02-arquitectura.md` |
| Conocer los 4 microservicios y sus bases | `docs/03-microservicios.md` |
| Crear/alterar entidades o tablas | `docs/04-modelo-datos.md` |
| Agregar o modificar un endpoint | `docs/05-endpoints.md` + `skills/SKILL-endpoint.md` + `rules/RULES-stack.md` |
| Aplicar un patrón de diseño | `docs/06-patrones.md` + `skills/SKILL-caso-uso.md` |
| Autenticación/autorización/roles | `docs/07-seguridad.md` + `rules/RULES-seguridad.md` |
| Publicar o consumir eventos | `docs/08-eventos-kafka.md` + `skills/SKILL-evento.md` + `rules/RULES-eventos.md` |
| Planificación y dimensionamiento | `docs/10-planificacion.md` |
| Diseño general por épica e historias (14 UH) | `docs/11-epicas-historias.md` + repo `plan/sprint0/uh/` |
| Propuestas de tareas (detalle técnico por dominio, mapeado a épicas) | `tareas/01..05` (administración, parámetros, proveedor LLM, contratos, reportes) |
| Reportes, métricas, exportación | `docs/09-despliegue.md` + `skills/SKILL-reporting.md` (si existe) |
| Compilar, testear, docker | `skills/SKILL-build-test.md` + `skills/SKILL-despliegue.md` |
| Reglas que NUNCA se deben violar | `rules/RULES-invariantes.md` (leer siempre) |

## Orden recomendado para un agente que arranca

1. `docs/01-overview.md` (contexto).
2. `rules/RULES-invariantes.md` (no romper nada).
3. `rules/RULES-stack.md` (convenciones de código).
4. Recién ahí, el `docs/` + `skills/` específico de tu tarea.

## Convenciones de archivo

- Los `docs/*` describen **qué** y **por qué**. Los `rules/*` dicen **qué está prohibido/obligado**. Los `skills/*` dicen **cómo** se hace.
- Los `rules/*` tienen prioridad sobre cualquier preferencia de estilo del agente. Si un `docs/` y un `rules/` se contradicen, el `rules/` manda y hay que reportar la contradicción.
- **El BackOffice NO implementa cursos, desafíos ni usuarios** (otros equipos): no crees servicios ni entidades para esos dominios.
- Mantené este índice actualizado si agregás/renombrás/quitas archivos.