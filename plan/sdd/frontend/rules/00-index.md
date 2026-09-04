# Rules (Frontend) — Índice

> Restricciones imperativas: "hacé"/"no hagas". **Tienen prioridad** sobre el estilo del agente. Si un `docs/` contradice una rule, manda la rule y reportá la contradicción.

| Archivo | Contenido |
|---|---|
| `RULES-stack.md` | Stack y convenciones del front (Angular SSR, multirepos, Nginx) |
| `RULES-deeplinks-cache.md` | Fallback por prefijo, cache headers, no romper el SPA al recargar |
| `RULES-hidratacion.md` | Consistencia server/client en SSR |
| `RULES-sesion.md` | Cookies httpOnly, no tokens en JS, flujo de logout |
| `RULES-no-store-global.md` | No store global cross-app; servidor = fuente de verdad |

> Orden: `RULES-sesion.md` → `RULES-stack.md` → según tarea.