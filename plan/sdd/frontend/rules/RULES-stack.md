# RULES — Stack y convenciones (Frontend)

1. **Caso A obligatorio:** apps **Angular SSR** independientes por dominio en **multirepos**. No se crea un Shell central que consuma librerías de todos (eso es Caso B).
2. **Nginx como única entrada pública**, con rutas por prefijo (`/`→alumno, `/profesor`, `/backoffice`, `/api/*`→BFF). Roles: web server + reverse proxy + deep-links.
3. **BFF por experiencia** (BackOffice/Alumno/Profesor). El front **no** llama directo a microservicios.
4. **No tokens en JS:** la sesión viaja en cookie httpOnly; no uses localStorage para tokens.
5. **No store global cross-app** (NgRx es por app).
6. **UI desde `@tup/ui`**: no reimplementes componentes ni definas colores/tokens sueltos.
7. **Docker en dos etapas** (`node:20` compila → `nginx:alpine` sirve estáticos); configuración de Nginx con **plantilla + `envsubst`** (misma imagen para staging/producción). Nunca URLs/credenciales hardcodeadas en `nginx.conf`.
8. **Deep-link fallback al index de ESA app** (`try_files $uri $uri/ /backoffice/index.html`); nunca al index de otra app.
9. **Despliegue base: Rolling Update + Feature Flags**; Blue-Green solo si la cátedra exige cero downtime. CI/CD con GitHub Actions.
10. **El front no toca la base ni Kafka**; el BFF no contiene reglas de negocio.
11. **Mantener `sdd/frontend/` sincronizado** con cualquier cambio del plan/implementación.