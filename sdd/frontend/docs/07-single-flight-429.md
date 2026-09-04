# 07 — Peticiones duplicadas y manejo de 429

Objetivo: si el usuario dispara **múltiples peticiones iguales**, solo se ejecuta **UNA** hasta que complete; y se trata bien el **429 Too Many Requests**.

## Frontend — patrón single-flight (request dedup)

En el **interceptor HTTP**: si ya hay una petición **idéntica en vuelo** (método + URL + body), las siguientes **se unen a la misma promesa** en vez de disparar otra.

```text
Petición (método+URL+body)
   ¿existe idéntica EN VUELO?
   ├── Sí → unirse a la misma promesa
   └── No → disparar; liberar clave al completar
```

- Clave de dedup: `método + URL + body normalizado` (o `Idempotency-Key` si el backend la soporta).
- Evita doble submit (Crear curso, Guardar configuración, Importar padrón).
- Complemento UX: botones deshabilitados / estados de carga (el interceptor es la garantía real).

## Manejo de 429

- Leer el header **`Retry-After`** y esperar (backoff con jitter) antes de reintentar.
- **No** reintentar automáticamente operaciones no idempotentes (POST/PUT administrativos).
- Mostrar mensaje claro al usuario.
- El BFF puede **coalescer** peticiones idénticas (segunda barrera).

## Backend (refuerzo)

Rate limiting en Gateway (Bucket4j/Redis), 429 con `Retry-After` + Idempotency Keys en operaciones críticas.

> Fuente: `frontend_plan_comunicacion.md` §8 · `sdd/backend/docs/05-endpoints.md`.