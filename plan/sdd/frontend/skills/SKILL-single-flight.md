# SKILL — Single-flight en el interceptor HTTP

Implementá el dedup de peticiones idénticas en el interceptor del `HttpClient`.

## Idea

```ts
// request-map.ts
const inFlight = new Map<string, Observable<unknown>>()

export function requestKey(req): string {
  return `${req.method}:${req.urlWithParams}:${JSON.stringify(req.body ?? null)}`
}

// en el interceptor:
const key = requestKey(req)
const existing = inFlight.get(key)
if (existing) {
  // unirse a la promesa ya en vuelo (misma instancia de Observable)
  return existing as HttpInterceptorFn // retornar como handle compartido
}
// crear, registrar y liberar al completar:
const done = finalize(() => inFlight.delete(key))
const obs = next.handle(req).pipe(done)
inFlight.set(key, obs)
return obs
```

> Detalle de implementación: si `next.handle` retorna un Observable, compartí el mismo observable con `shareReplay(1)` (o un `BehaviorSubject` manual) para que los que se unen reciban el mismo resultado sin disparar otra request.

## Manejo de 429

En el mismo interceptor:

```ts
error: (err) => {
  if (err.status === 429 && retryable) {
    const retryAfter = +err.headers.get('Retry-After') * 1000 || 1000
    return timer(retryAfter + jitter()).pipe(mergeMap(() => next.handle(req)))
  }
  // NO reintentar operaciones no idempotentes (POST/PUT) automáticamente
}
```

## Complemento UX

- Botones con estado de carga (`disabled` mientras la petición está en vuelo).
- Mensaje claro ante 429: "Demasiadas solicitudes, esperá unos segundos".

> Ver `docs/07-single-flight-429.md`.