# RULES — Sesión (cookies, login, logout)

1. **Cookie httpOnly + Secure + SameSite** con dominio compartido: el JS **no** la puede leer ni manipular. No intentes acceder al JWT desde JS.
2. **No guardes tokens en `localStorage`/`sessionStorage`** (riesgo XSS).
3. **Login único:** una sola pantalla/flujo (Identity) + **2FA**. No implementes autenticación propia por app.
4. **Logout:** endpoint de logout que invalida sesión + borra cookie + dispara `auth:logout` (para que las otras apps/pestañas limpien estado).
5. **Expiración:** si el BFF devuelve 401 → redirigir a login **conservando la navegación intentada** y emitir `auth:session-expired`.
6. El contrato de sesión lo define el **equipo BackOffice** (Identity): el front solo lo consume. Si necesitás un comportamiento de sesión nuevo, coordinalo, no lo inventes.