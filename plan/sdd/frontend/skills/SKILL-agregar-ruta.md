# SKILL — Agregar una ruta/vista (app Angular del Caso A)

1. **Ruta:** definila en el router de la app (`app.routes.ts`) — la app es independiente; no toques el router de otra app.
2. **Vista:** creá el componente en la app; usá componentes de `@tup/ui` (ver `SKILL-tup-ui.md`).
3. **Datos:** la vista pide datos **al BFF de su experiencia** (no a microservicios directo). Si el dato inicial debe estar en el SSR, el BFF lo provee para el render inicial.
4. **Sesión:** no leas la cookie; el BFF valida. Si la sesión expiró, el BFF devuelve 401 → seguí el flujo de `rules/RULES-sesion.md`.
5. **Submit/acciones:** usá single-flight para operaciones (ver `SKILL-single-flight.md`).
6. **Validación de rutas con parámetros:** ej. `/backoffice/cursos/:id` — recordá que al **recargar** esa ruta, Nginx debe servir el index de la app (ver `SKILL-nginx.md`).