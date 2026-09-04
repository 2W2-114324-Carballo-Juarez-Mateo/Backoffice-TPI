# NotebookLM — Video del Frontend BackOffice (arquitectura y despliegue)

> **Antes:** cargá la fuente `Backoffice-Fuente-NotebookLM.md`.
> **Cómo:** usá **Video Overview** y pegá este prompt.

---

## Prompt (corto)

Generá un **Video Overview** de la fuente "Backoffice-Fuente-NotebookLM" centrado en **la propuesta de frontend del BackOffice (Tema 12)**: Caso A (apps Angular SSR + Nginx), BFF por experiencia y despliegue alineado a la consigna de Arquitectura y Despliegue. En español, didáctico, con **analogías** (Nginx = recepcionista que decide a qué oficina enviarte; BFF = mesero que te trae todo el pedido de una; cookie httpOnly = llave que viaja sola).

**Estructura sugerida (5 bloques):**
1. **Postura Front (Caso A)**: apps Angular SSR independientes por dominio en multirepos, integradas por **Nginx** bajo un mismo dominio (`/` alumno, `/backoffice`, `/profesor`, `/api/*`). Por qué: autonomía entre equipos y aislamiento de fallas.
2. **Nginx — los 3 roles**: **servidor web** (sirve el Angular compilado en `nginx:alpine`), **reverse proxy** (`/api/*` → BFF), **deep-links** (`try_files ... /backoffice/index.html` — el index de esa app). Cache: index no-cache, assets inmutables.
3. **BFF BackOffice (del equipo BackOffice)**: recibe la cookie httpOnly, valida en Identity (T01), arma el contexto de autorización (usuario/rol/alcance) y **agrega** las respuestas de Administration, Reporting y T02 para cada pantalla. Sirve datos al SSR. **Sin reglas de negocio**; el front nunca habla con Kafka ni con la base.
4. **Build y despliegue**: Docker en **2 etapas** (`node:20` compila → `nginx:alpine` sirve, sin Node en producción), **`envsubst`** (la misma imagen sirve staging/producción cambiando `${BFF_URL}`), **load balancing** con `upstream` (algoritmos: Round Robin, Weighted, Least Connections, IP Hash). **Estrategias evaluadas**: elegimos **Rolling Update + Feature Flags** (Blue-Green como alternativa); **Canary, A/B y Shadow descartados** porque exigen balanceo por porcentaje/monitoreo en tiempo real o duplicar tráfico sin duplicar efectos. **Herramientas**: CI/CD GitHub Actions, secretos por variables de entorno, imagen inmutable por release y monitoreo/rollback con healthchecks + tags.
5. **Con quién se conecta y multitenancy**: vía BFF → **API Gateway (T01)** → Administration, Reporting, T02 y lecturas. La UI usa un **selector de alcance** (curso puntual o **ALL** para ADMIN); el BFF resuelve el tenant (`app.current_course`) desde la sesión validada — el front nunca manda un `course_id` suelto.

Mantené fidelidad a la fuente; no inventes requisitos.