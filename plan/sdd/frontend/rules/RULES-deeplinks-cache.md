# RULES — Deep-links y cache (no romper el SPA)

1. **Fallback por prefijo:** cada app tiene su `location` en Nginx con fallback a **su propio index** (SSR por `proxy_pass` o `try_files $uri $uri/ /<app>/index.html`). **Nunca** apuntes el fallback de una app al index de otra.
2. **`index.html` → `Cache-Control: no-cache`** (siempre revalidar).
3. **Assets con hash (`.js`/`.css`) → `immutable` + cache largo.**
4. Si una pestaña vieja recarga y falla un chunk dinámico → **hard reload** (no dejar pantalla en blanco).
5. No dependas de estado en memoria que deba sobrevivir a una recarga: tras recargar, la app re-bootstrapa y pide datos al BFF.
6. Si una app está caída, Nginx muestra **degradación controlada** (página de aviso) sin romper al resto.