# 02 — Frontend → BFF → microservicios

## Decisión: BFF por experiencia

| Alternativa | Decisión |
|---|---|
| BFF compartido | ❌ cuello de botella |
| BFF por dominio | ❌ el front llama a muchos |
| **BFF por experiencia** (BackOffice, Alumno, Profesor) | ✅ **elegido** |

Cada experiencia agrega lo que su pantalla necesita. En Caso A, cada equipo es dueño del BFF de su app — el **BFF de BackOffice es del equipo BackOffice**.

## Flujo

```text
Frontend app → BFF → Identity (validar sesión) → microservicios (con contexto de autorización) → respuesta agregada
```

## Responsabilidades del BFF

- Recibe la **cookie httpOnly** (no legible por JS) y valida la sesión contra Identity.
- Convierte la sesión en **contexto de autorización** (usuario, rol, alcance).
- **Agrega** respuestas de varios microservicios para la pantalla.
- Oculta la red de microservicios al front; sirve datos al **SSR**.
- **No** almacena reglas de negocio: solo orquesta y adapta contratos.

## Autenticación BFF → servicios (concepto)

El BFF no reenvía credenciales del usuario al navegador; internamente usa un contexto de autorización (token de servicio + identidad del usuario). Detalles a definir en la coordinación/LL.

> Fuente: `frontend_plan_comunicacion.md` §2.