# Tarea F2 — App Angular SSR (shell BackOffice)

> **Sprint 1 · Talla L · ~5 persona-días** · Caso A (multirepos + Nginx)

## 1. Objetivo
Crear la **app Angular SSR** de la consola admin (`backoffice-ssr`): shell con layout, login/sesión, router por módulos y bootstrap con datos del **BFF** (misma fuente server/client → sin hydration mismatch).

## 2. Alcance
- **In:** bootstrap SSR, layout (barra superior con `@tup/ui`), login + 2FA, guardas por rol, manejo de 401/429, deep-links.
- **Out:** NO define tokens/colores propios (usar `@tup/ui`); NO usa store global cross-app.

## 3. Requerimientos vinculados
RF-USR-* (roles), RF-CFG-* (config), RF-RPT-* (reportes).

## 4. Diseño técnico
- **Stack:** Angular SSR (materia Front), `@tup/ui` (tokens + componentes), `@tup/contracts` (tipos OpenAPI), NgRx por app.
- **Sesión:** cookie httpOnly (propiedad de Identity); el **401 del BFF → redirect a login** conservando el intento + evento `auth:session-expired`.
- **single-flight** en el interceptor (dedup de peticiones idénticas) + manejo de **429** con `Retry-After`.
- **Recarga/deep-links:** rutas internas resueltas por el router de Angular; Nginx sirve `index.html` de esa app.
- **Selector de alcance:** componente que elige **curso puntual o todos (ALL)** y lo envía en el contexto (nunca un `course_id` suelto).

## 5. Pruebas
SSR (primer paint), login/logout/2FA, guardas por rol (PROFESOR vs ADMIN), deep-link `/backoffice/...`, 429/single-flight, recarga con estado perdido (rebootstrap vía BFF).

## 6. DoD
- [ ] SSR bootstrap correcto (misma data server/client).
- [ ] Login + 2FA + logout; 401 → login conservando intento.
- [ ] Guardas por rol y selector de alcance (curso/ALL).
- [ ] Build Docker 2 etapas OK.

> Reglas: `rules/RULES-stack.md` · `docs/01-overview.md` · `docs/04-sesion.md`.