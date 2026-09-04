# SKILL — Usar/publicar `@tup/ui`

## Consumir (en una app)

1. Agregá la dependencia: `npm i @tup/ui` (versión semver).
2. Usá componentes y tokens: `@import '@tup/ui/tokens.css';` + los componentes Angular.
3. **No** redefinir colores, radios, spacing sueltos; usá los tokens.

## Publicar (solo el dueño de la librería)

1. Agregá/modificá el componente o token en `@tup/ui`.
2. **Versioná con semver**: cambio incompatible (API visual/estructural) → **major**; aditivo → minor; fix → patch.
3. Publicá el paquete (`npm publish`) y actualizá el changelog.
4. El **CI** valida: la librería compila y las apps consumen versiones compatibles.

## Reglas de consistencia

- Un componente que falta se agrega a `@tup/ui` (con release), **no** se hackea localmente en una app.
- Las apps consumen; no mantienen forks de la librería.

> Ver `docs/05-ui.md`.