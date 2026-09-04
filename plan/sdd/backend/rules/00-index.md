# Rules (Backend) — Índice

> Las **Rules** son restricciones imperativas: "hacé" / "no hagas". **Tienen prioridad** sobre cualquier preferencia de estilo del agente. Si un `docs/` contradice una rule, manda la rule y reportá la contradicción.

| Archivo | Contenido | Cuándo aplica |
|---|---|---|
| `RULES-stack.md` | Stack, versiones, estructura de capas, convenciones de código | **Siempre que escribas código** |
| `RULES-invariantes.md` | Reglas de negocio que NUNCA se violan | **Siempre** |
| `RULES-eventos.md` | Envelope, topic/consumer groups, Outbox, idempotencia | Cuando publiques/consumas eventos |
| `RULES-seguridad.md` | Autorización, secretos, qué NO exponer | **Siempre que toques seguridad/endpoints** |

> Orden sugerido de lectura para un agente que arranca: `RULES-invariantes.md` → `RULES-stack.md` → el resto según tarea.