# 03 — Compartir información entre frontends

## Mecanismos

### Custom Events (preferido para avisos)

| Evento (concepto) | Cuándo | Consumidores |
|---|---|---|
| `auth:logout` | El usuario cerró sesión | Todas las apps limpian estado y redirigen |
| `auth:session-expired` | BFF devolvió 401 | App activa redirige a login conservando intento |
| `data:changed` | Un dominio actualizó datos visibles en otra app | Apps interesadas refrescan |

- Payload mínimo y versionable; nombres/contratos a fijar en la coordinación.
- Son **notificaciones**, no la fuente de verdad.

### Storage

- `localStorage`/`sessionStorage` (mismo origen) para **preferencias y caché ligera** no sensibles.
- **Nunca** tokens ni datos sensibles (XSS): el token va en cookie httpOnly.

### Store compartido

- **No** se usa store global entre apps distintas (frágil, acopla). Cada app mantiene su NgRx/local state.
- **Regla:** el estado de negocio vive en el servidor; cada app lo consulta. Eventos/storage solo notifican o cachean.

> Fuente: `frontend_plan_comunicacion.md` §3.