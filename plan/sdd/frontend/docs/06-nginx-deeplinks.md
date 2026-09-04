# 06 — Nginx: entrada pública, deep-links y recarga

## Enrutamiento por prefijo

| Ruta | Destino |
|---|---|
| `/` | App Alumno / landing (SSR) |
| `/profesor` | App Profesor (SSR) |
| `/backoffice` | App BackOffice (SSR) |
| `/api/*` | BFF / Gateway (proxy) |
| `/assets/*` | Estáticos de cada app |

## Deep-links y recarga (evitar corromper el SPA)

Al recargar una ruta client-side (ej. `/backoffice/cursos/10`), Nginx debe servir la entrada **de esa app**; si el fallback está mal, sirve el index de otra app o 404.

**Fallback por prefijo:**

```nginx
location /backoffice/ {
    proxy_pass http://backoffice-ssr:4000;      # SSR
}
location /backoffice/ {                          # respaldo estático
    try_files $uri $uri/ /backoffice/index.html;
}
```

> Regla de oro: el fallback apunta al index **de esa app**, nunca al de otra.

**Cache headers:** `index.html` → `no-cache`; assets con hash → `immutable` + cache largo (evita "chunks rotos" al recargar con una pestaña vieja).

**Chunk load error → hard reload:** si falla un import dinámico (pestaña vieja), forzar recarga completa.

**Estado tras recarga:** se pierde el estado en memoria (NgRx) — esperado; el servidor es la fuente de verdad y la app re-bootstrapa vía BFF.

> Fuente: `frontend_plan_comunicacion.md` §6 y §6bis.