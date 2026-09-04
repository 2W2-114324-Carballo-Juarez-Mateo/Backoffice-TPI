# 04 — Sesión, login y logout

Mecánica **conceptual** (nombres/endpoints concretos a definir en la coordinación/LL).

- **Cookie httpOnly + Secure + SameSite**, con dominio compartido → viaja sola a todas las apps del Caso A.
- **Login único:** una sola pantalla/flujo de login (Identity). Emite el JWT en la cookie.
- **2FA:** obligatorio (RNF-02 del PRD); vive en Identity.
- **Logout:** endpoint que invalida la sesión + borra la cookie + dispara `auth:logout`.
- **Expiración:** 401 del BFF → la app redirige a login conservando el intento; se emite `auth:session-expired`.

## Propiedad

Este contrato de sesión lo define el **equipo BackOffice** (Identity Service). El front solo lo consume — el navegador guarda y envía la cookie automáticamente; JS **no** puede leerla (httpOnly).

> Fuente: `frontend_plan_comunicacion.md` §4.