# SKILL — Configurar Nginx (web server, reverse proxy, deep-links, load balancing)

## Regla por app

Cada app tiene su `location` con fallback a **su propio** index:

```nginx
# BackOffice (SSR)
location /backoffice/ {
    proxy_pass http://backoffice-ssr:8095;
}

# Respaldar el caso estático / fallback profundo:
location /backoffice/ {
    try_files $uri $uri/ /backoffice/index.html;
}

# Alumno (raíz)
location / {
    proxy_pass http://alumno-ssr:8094;
}

# API → BFF/Gateway (reverse proxy)
location /api/ {
    proxy_pass http://bff-backoffice:8094;
}
```

## Cache

```nginx
location /backoffice/ {
    # index.html: no cachear
    add_header Cache-Control "no-cache" always;
}
location /backoffice/assets/ {
    # assets con hash: inmutables
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

## Config dinámica (envsubst)

```nginx
# nginx.conf.template
location /api/ {
    proxy_pass ${BFF_URL};
}
```

```bash
envsubst < /etc/nginx/templates/nginx.conf.template > /etc/nginx/conf.d/default.conf
nginx -g 'daemon off;'
```

## Load balancing (varias instancias)

```nginx
upstream bff_backend {
    least_conn;
    server bff-backoffice-1:8094;
    server bff-backoffice-2:8094;
}
location /api/ {
    proxy_pass http://bff_backend/;
}
```

## Verificaciones

- Recargá una ruta profunda (`/backoffice/cursos/10`) → debe cargar el index de **BackOffice**, no 404 ni otra app.
- `GET /api/...` → responde el BFF (no 404 del servidor web).
- Si una app está caída → página de degradación sin romper el resto.
- Headers de seguridad: CSP, HSTS, `X-Content-Type-Options`.
- La misma imagen sirve staging/producción cambiando `${BFF_URL}` (sin re-build).

> Reglas: `rules/RULES-deeplinks-cache.md` · `rules/RULES-stack.md`.