# Frontend — Análisis de Arquitectura y Postura del Grupo

> **Consigna de docentes (próxima clase):** investigar y traer postura sobre cómo construir la arquitectura de la plataforma. Analizar **Caso A** (apps Angular SSR independientes en multirepos detrás de Nginx) vs **Caso B** (App Shell Angular central + librerías), más los temas de BFF, comunicación entre frontends, sesión, UI compartida y marketplace de plugins.
>
> **Postura del grupo: Caso A.** Este documento desarrolla por qué, con sus costos y riesgos reconocidos.
>
> **Plan con decisiones concretas:** ver `frontend_plan_comunicacion.md` (BFF por experiencia, contrato de sesión, `@tup/ui`, Nginx, single-flight/429 y marketplace).

---

## 1. El problema que hay que resolver

La plataforma se divide en microservicios por dominio (BackOffice, Alumno, Profesor, IA, Gamificación...). Ese mismo corte debería reflejarse en el frontend: **cada dominio tiene su app**. El dilema es cómo integrar esas apps sin romper la experiencia ni generar fricción entre equipos.

Los dos casos que plantearon los docentes son dos formas extremas de resolverlo:

| | **Caso A** | **Caso B** |
|---|---|---|
| Integración | Enrutamiento por URL (Nginx) | Runtime centralizado (Shell) |
| Propiedad del código | Multirepos independientes | Un repo de apps + librerías publicadas |
| Acoplamiento entre equipos | Bajo | Alto |
| Release | Independiente por app | Coordinado con el Shell |
| Sensación de uso | Navegación por apps (puede sentirse como apps separadas) | Una sola SPA continua |

---

## 2. Caso A — Apps Angular SSR en multirepos detrás de Nginx

### 2.1 Cómo funciona

- Cada dominio mantiene su **repositorio**, su build y su pipeline de deploy.
- **Nginx** actúa como entrada pública: presenta todas las apps bajo un mismo dominio usando prefijos de ruta.
  - `https://plataforma.edu.ar/` → app Alumno (o landing)
  - `https://plataforma.edu.ar/backoffice` → app BackOffice
  - `https://plataforma.edu.ar/profesor` → app Profesor
  - `https://plataforma.edu.ar/api/*` → Gateway/BFF
- **Angular SSR** renderiza el HTML inicial en el servidor (mejor primer paint y SEO donde aplique) y luego Angular **hidrata** en el navegador.

### 2.2 Cómo se resuelven las preocupaciones compartidas sin acoplar repositorios

**Sesión (cookies):**
- Todas las apps viven bajo el **mismo dominio**, por lo que una **cookie httpOnly** con `Domain=plataforma.edu.ar` se envía automáticamente a todas.
- El login se hace una sola vez (endpoint de Identity). El backend emite JWT en cookie httpOnly (con `Secure` y `SameSite=Lax/Strict`). Cada app, al arrancar, valida el token contra el BFF/Gateway.
- Resultado: **sesión compartida sin escribir una sola línea entre repos**, solo se acuerda el contrato de cookie.

**Navegación:**
- La navegación entre apps es por **URLs** (`routerLink` externo / `href`). Cada app enlaza a las demás con rutas completas. Deep links funcionan porque SSR renderiza contenido inicial.
- Cada app incluye una **barra superior común** (componente de la librería UI) con links a las otras apps. No hay shell central que ejecutar.

**Estilos:**
- **Librería CSS/UI compartida** publicada como paquete npm versionado (`@tup/ui`): design tokens (variables CSS), componentes, tipografía, paleta. Es una dependencia de **build-time**, no un repo compartido: no viola el multirepo.

**Contratos:**
- Cada app habla con **su BFF** (o el dominio correspondiente). Los contratos se documentan en OpenAPI y los tipos compartidos van en `@tup/contracts` (paquete versionado). Se acuerda una convención global de eventos y errores.

**Estado:**
- No hay store global entre apps. Cada app tiene su NgRx/local state.
- Para comunicarse entre apps: **Custom Events** (`window.dispatchEvent(new CustomEvent(...))`) para avisos puntuales (ej. "sesión expiró", "XP actualizado"), y **Storage** (localStorage/sessionStorage, mismo origen) para preferencias no sensibles.
- **Regla de oro:** el estado de negocio vive en el servidor (microservicios); cada app lo consulta. El frontend no duplica verdad de negocio.

---

## 3. Caso B — App Shell Angular central + librerías

### 3.1 Cómo funciona

- Una **app Shell** concentra navegación, layout y runtime.
- Cada equipo **publica una librería versionada** que el Shell consume.
- El Shell se encarga de cargar, enrutar y componer las funcionalidades de cada equipo.

### 3.2 Costos que reconocemos

- **Coordinación de releases:** el Shell y todas las librerías deben versionar juntos (matriz de compatibilidad). Un equipo que publica una versión rompe a los demás.
- **Centralización = punto de falla:** una app de equipo con un bug puede tumbar la experiencia completa. Mitigación clásica (module federation / carga en runtime) agrega complejidad considerable.
- **Fricción entre grupos:** cada decisión de UI/estado/navegación del Shell se discute entre todos los equipos; es el escenario de "peleas" que queremos evitar.
- **Menos autonomía:** un equipo no puede desplegar sin coordinarse con el dueño del Shell.

---

## 4. Por qué conviene el Caso A (postura del grupo)

### 4.1 Autonomía total de cada equipo

En un trabajo integrador donde cada grupo es dueño de su dominio, el Caso A permite que **cada equipo avance a su ritmo**: su repo, su build, su deploy, sus pruebas. No existe el paso "esperar al equipo del Shell".

### 4.2 Aislamiento de fallas

Si la app de BackOffice se cae o rompe en producción, **las demás apps siguen funcionando**. Nginx sigue enrutando al resto. En el Caso B, un bug en una librería puede tumbar toda la SPA.

### 4.3 Cero conflictos de repositorio

Los equipos **no comparten repos ni código**: no hay merge conflicts, ni alguien que rompa el build del otro por accidente. Es el argumento operativo más fuerte para un proyecto con varios grupos independientes.

### 4.4 Independencia de versionado y despliegue

Cada app publica su propia versión. Un equipo puede hacer rollback de su app **sin tocar a nadie**. Las librerías compartidas (`@tup/ui`, `@tup/contracts`) se versionan con semver y los equipos las actualizan cuando quieren.

### 4.5 SSR como valor agregado

El SSR de cada app mejora el primer paint y el SEO de las partes públicas (landing, o lo que sea público). Es un beneficio que el Caso B puro (SPA client-side) no entrega sin trabajo extra.

### 4.6 Infraestructura simple y predecible

El único componente compartido es **Nginx** (y el Gateway/BFF). Una sola pieza de configuración, fácil de entender y de defender: "cada dominio es una app; Nginx las une por URL".

### 4.7 Riesgos y costos reconocidos (para defender con honestidad)

| Riesgo | Mitigación |
|---|---|
| **Consistencia visual** entre apps | Librería `@tup/ui` + tokens; regla de no sobreescribir estilos localmente |
| **Duplicación de bootstrap/config** por app | Build compartido vía monorepo de tooling o template inicial; cada app copia el patrón |
| **Coordinación de sesión** | Contrato único de cookie + endpoint de login/logout común; validación en el BFF |
| **Experiencia de navegación** (se siente como apps separadas) | Barra común + enlaces consistentes; transiciones aceptables para apps administrativas |
| **Multiplicidad de BFF** | Un BFF por experiencia (alumno/profesor/backoffice), no por microservicio |
| **Duplicación de lógica en BFFs** | Librería de helpers compartida (solo código de infra, no de negocio) |

> **Conclusión de la postura:** el Caso A optimiza lo que este proyecto más necesita — **autonomía entre equipos y aislamiento de fallas** — a cambio de consistencia visual y experiencia unificada, que se resuelven con librería UI compartida y buenos contratos. El Caso B optimiza la experiencia unificada pero centraliza coordinación y riesgo, que es justamente el punto más frágil de un proyecto con muchos grupos.

---

## 5. Temas de investigación pedidos por los docentes

### 5.1 Frontend → BFF → microservicios

El **BFF (Backend for Frontend)** es un intermediario server-side propiedad del frontend. Resuelve:
- **Agregación:** arma la respuesta que la pantalla necesita (ya no un DTO crudo de cada microservicio).
- **Seguridad:** recibe la cookie del usuario, la convierte en contexto de autorización y llama a los microservicios con credenciales de servicio.
- **Contratos:** oculta la red de microservicios al frontend (el front solo conoce el BFF).
- **SSR:** en Caso A, el BFF puede proveer los datos para el render inicial del servidor.

### 5.2 Alternativas de BFF

| Alternativa | Pros | Contras |
|---|---|---|
| **BFF compartido** (uno para todas las apps) | Simple, un solo deploy | Cuello de botella, acopla dominios, un cambio toca a todos |
| **BFF por dominio** (uno por microservicio) | Alineado con microservicios | El frontend termina llamando a muchos BFFs (misma complejidad que sin BFF) |
| **BFF por experiencia** (alumno, profesor, backoffice) | Cada experiencia pide lo que necesita; esconden la orquestación | Requiere definir "experiencias" |

**Postura:** BFF **por experiencia**. BackOffice, Alumno y Profesor son experiencias distintas con consumos distintos; cada una agrega lo que su pantalla necesita. En Caso A, cada app puede tener su propio BFF (o compartir el del Gateway si el volumen lo justifica).

### 5.3 Cómo compartir información entre frontends

- **Custom Events:** `window.dispatchEvent(new CustomEvent('auth:logout', { detail }))` + `window.addEventListener`. Ideal para avisos puntuales y descoplados. No persisten.
- **Store compartido:** útil **dentro** de una misma app. Entre apps distintas (Caso A) es complejo y frágil; si se necesita, se hace vía un pequeño iframe "broker" o vía el servidor. **No recomendado como mecanismo cross-app**.
- **Storage:** `localStorage`/`sessionStorage` del mismo origen. Sirve para preferencias y caché ligera. **No** para tokens sensibles (XSS) — el token va en cookie httpOnly.
- **Regla:** para estado que importa (XP, ranking, sesión), el **servidor es la fuente de verdad**; los eventos/storage son solo notificaciones o caché.

### 5.4 Cookies, sesión, login y logout entre apps

- **Login:** único punto (app de auth o endpoint de Identity). Emite JWT en cookie **httpOnly + Secure + SameSite**.
- **Sesión compartida:** como todas las apps del Caso A viven bajo el mismo dominio, la cookie viaja sola a todas. Cada app valida el token al arrancar (por su BFF).
- **Logout:** endpoint de logout que invalida la sesión y borra la cookie; además se dispara un Custom Event `auth:logout` para que las otras apps (abiertas en otras pestañas) limpien su estado local.
- **Expiración:** si el token expira, el BFF devuelve 401; la app redirige al login conservando el intento de navegación.

### 5.5 Librería CSS/UI compartida y consistencia visual

- Paquete `@tup/ui` con **design tokens** (variables CSS: colores, spacing, tipografía, radii) + componentes (botones, tablas, forms, navbar, badges).
- Reglas: los componentes se consumen **solo** desde la librería; no se definen estilos propios que rompan el token. Semver: cambios breaking → major.
- CI valida que la librería compile y que las apps consuman versiones compatibles.

### 5.6 Rol de Nginx como entrada pública y enrutador técnico

- **Reverse proxy** con terminación TLS (HTTPS).
- **Servir estáticos + SSR:** entrega el HTML renderizado (proxy a los servidores SSR de cada app) y los assets de cada app por prefijo de ruta.
- **Enrutamiento técnico:** `/backoffice/*` → app BackOffice; `/alumno/*` → app Alumno; `/api/*` → Gateway/BFF.
- **Edge hardening:** compresión, cache de assets inmutables, rate limiting básico, headers de seguridad (CSP, HSTS), y aislamiento (si una app está caída, redirige a una página de degradación controlada sin romper al resto).

### 5.7 Marketplace de plugins para la TUP orientado a agentes de IA

Contexto: un marketplace donde agentes (Claude, Codex, OpenCode, Gemini, Copilot) publican/consumen plugins de la TUP. Qué necesita para ser seguro:

| Capacidad | Qué implica |
|---|---|
| **Publicar** | Manifesto (id, nombre, versión, autor, descripción, tags, permisos declarados), SDK del agente al que apunta |
| **Versionar** | Semver, versiones inmutables (no reescribir una versión publicada), canales stable/beta |
| **Descubrir** | Catálogo con búsqueda por agente/capacidad/rating, score de confianza |
| **Instalar/actualizar** | Resolución de dependencias, compatibilidad con la versión del agente, rollback a versión anterior, control de actualizaciones automáticas |
| **Validar de forma segura** | Análisis estático, sandbox de ejecución, validación del esquema de tools (p. ej. MCP/Model Context Protocol), revisión humana, firma de artefactos + checksums (provenance) |
| **Permisos y seguridad** | Least privilege, aprobación explícita del usuario antes de que el plugin toque datos o acciones, límites de cuota/rate, auditoría de ejecución, scanning de secretos, política anti-código malicioso |

**Para agentes de IA en particular:** el plugin expone un **contrato de herramientas (tools)** — idealmente estandarizado vía **MCP** — que el agente invoca. El marketplace debe validar ese contrato (entradas/salidas tipadas) y el **set de permisos** que declara, para que el usuario confirme antes de ejecutar.

---

## 6. Decisión del grupo (resumen para defender)

> Elegimos **Caso A**: apps Angular SSR independientes por dominio en multirepos, integradas por Nginx, con sesión compartida por cookie del dominio, UI compartida vía librería `@tup/ui`, BFF por experiencia, y comunicación entre apps por Custom Events + Storage (sin store global). Reconociendo los costos (consistencia, duplicación de bootstrap, coordinación de sesión), lo elegimos porque **maximiza la autonomía entre grupos y el aislamiento de fallas**, que son los dos riesgos más altos de un proyecto integrador con equipos independientes.