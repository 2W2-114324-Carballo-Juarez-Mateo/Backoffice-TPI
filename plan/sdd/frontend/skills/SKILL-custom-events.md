# SKILL — Custom Events entre apps

Comunicación desacoplada entre apps del mismo dominio (o pestañas) con `window` events.

## Disparar

```ts
window.dispatchEvent(new CustomEvent('auth:logout', { detail: {} }))
window.dispatchEvent(new CustomEvent('data:changed', { detail: { entity: 'ranking', courseId } }))
```

## Escuchar

```ts
window.addEventListener('auth:logout', handler)
// limpiar en ngOnDestroy:
window.removeEventListener('auth:logout', handler)
```

## Reglas

- Eventos solo **notifican**; no transportes estado de negocio (ver `rules/RULES-no-store-global.md`).
- Payload mínimo y versionable (agregar campos, no renombrar).
- Los nombres (`auth:logout`, `auth:session-expired`, `data:changed`) se fijan en la coordinación del equipo; no inventes nombres nuevos sin avisar.
- Para datos que importan (XP, ranking), la app afectada **refresca desde el servidor** al recibir el evento, no lee el payload como verdad.