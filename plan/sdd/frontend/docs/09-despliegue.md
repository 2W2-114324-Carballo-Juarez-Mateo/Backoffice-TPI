# 09 — Arquitectura y Despliegue (Nginx, Docker, estrategias)

> Alineado a la consigna de Front — Unidad 1 (Arquitectura y Despliegue) y al plan de Back del BackOffice. Caso A: apps Angular SSR + Nginx + BFF por experiencia.

## 1. Componentes del BackOffice (materia Front)

| Componente | Rol |
|---|---|
| **App Angular SSR** (`backoffice-ssr`) | Consola admin: PAR, proveedores LLM, reportes, panel, métricas, export, alertas |
| **Nginx de plataforma** | Servidor web (estáticos/SSR) + reverse proxy (`/api/*` → BFF) + deep-links + headers de seguridad |
| **BFF BackOffice** (`bff-backoffice`) | Valida cookie en Identity, agrega Reporting + Administration + T02, sirve datos al SSR. Sin reglas de negocio |

El BFF y la app son de la **materia Front**; **no** suman microservicios de dominio (el backend sigue con 2 servicios propietarios).

## 2. Conexiones

`Front → BFF → API Gateway (T01) → microservicios`

- **API Gateway (T01)**: única puerta de `/api`.
- **Identity (T01)**: login, sesión (cookie httpOnly), roles, 401 → login.
- **Administration & Configuration**: PAR-01..24, proveedores LLM, evaluador, golden set.
- **Reporting & Analytics**: panel, reportes docentes, métricas/CSAT, export, alertas.
- **Cursos / Matrícula (T02)**: listar cursos (selector de tenant) y validar pertenencia.
- **Lecturas 02/04/05/07/08/10**: solo si el panel lo requiere.

**Reglas:** el front **no** habla con Kafka (eventos son backend-backend) ni con la base. El **multitenancy** lo resuelve el BFF (selector curso puntual o `ALL`) seteando `app.current_course` desde la sesión validada.

## 3. Nginx (web server + reverse proxy + deep-links)

```nginx
server {
    listen 80;
    add_header X-Content-Type-Options nosniff always;
    add_header Strict-Transport-Security "max-age=31536000" always;

    location /backoffice/ {                 # SSR
        proxy_pass http://backoffice-ssr:8095;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    location /backoffice/ {                 # deep-link fallback (index DE ESA app)
        try_files $uri $uri/ /backoffice/index.html;
    }
    location /backoffice/assets/ {          # assets con hash → inmutables
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    location /api/ {                        # reverse proxy → BFF
        proxy_pass ${BFF_URL};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

> `try_files ... /backoffice/index.html` evita el 404 al recargar rutas internas del router (regla de oro: el index de esa app).

## 4. Docker — build en dos etapas + `envsubst`

```dockerfile
FROM node:20 AS build
WORKDIR /app
COPY . .
RUN npm install && npm run build

FROM nginx:alpine
COPY --from=build /app/dist/backoffice /usr/share/nginx/html
COPY nginx.conf.template /etc/nginx/templates/nginx.conf.template
```

```bash
# arranque del contenedor
envsubst < /etc/nginx/templates/nginx.conf.template > /etc/nginx/conf.d/default.conf
nginx -g 'daemon off;'
```

- El template lleva `${BFF_URL}` sin resolver → **misma imagen para staging/producción**.
- `docker-compose`: `frontend-backoffice` (solo puerto 8080:80 visible) + `bff-backoffice` (expose interno 4100) con `healthcheck`; `depends_on` con `condition: service_healthy`.

## 5. Load balancing

```nginx
upstream bff_backend {
    least_conn;
    server bff-backoffice-1:8094;
    server bff-backoffice-2:8094;
}
location /api/ { proxy_pass http://bff_backend/; }
```

**Algoritmos de Nginx (consigna):**

| Algoritmo | Directiva | Cómo elige | Cuándo |
|---|---|---|---|
| Round Robin | (por defecto) | Orden circular por turno | Capacidad similar |
| Weighted | `weight=3` | Mayor `weight` → más proporción | Capacidad distinta |
| Least Connections | `least_conn;` | Menos conexiones activas | Carga despareja |
| IP Hash | `ip_hash;` | Misma instancia por IP del cliente | Sticky sessions |

Nginx retira instancias que dejan de responder (fallos de conexión); health checks de aplicación → orquestador/healthchecks activos. Para el TP (~450 usuarios, mitad activos) alcanza 1 instancia.

## 6. Despliegue: estrategias evaluadas

| Estrategia | Nuestra decisión |
|---|---|
| **Rolling Update** | ✅ **Base** (no duplica infra; versiones conviven sin romper contratos) |
| **Feature Flags** | ✅ **Complemento** (activar pantallas sin re-deploy) |
| **Blue-Green** | ⚠️ **Alternativa** (cero downtime; duplica infra) |
| **Canary** | ❌ Descartado (balanceo por % + monitoreo en tiempo real, no justificado) |
| **A/B Testing** | ❌ Descartado (mide negocio/UX, no aplica) |
| **Shadow** | ❌ Descartado (duplicar tráfico sin duplicar efectos = complejo y riesgoso) |

### 6.1 Herramientas del despliegue

| Categoría | Postura |
|---|---|
| Infraestructura e inmutabilidad | Imagen inmutable por release (Docker Compose); Terraform/Ansible si sobra tiempo |
| Pipelines CI/CD | GitHub Actions: build 2 etapas → tests → push imagen → deploy compose |
| Secretos y configuración | Variables de entorno vía `envsubst`; nunca credenciales en `nginx.conf`/repo |
| Monitoreo y rollback | healthchecks `service_healthy` + logs de Nginx + tags por versión; Prometheus/Grafana opcional |

## 7. Qué NO se hace

- Front no habla con la base ni con Kafka.
- BFF sin reglas de negocio (solo orquesta y adapta contratos).
- No se implementan otras experiencias (Alumno/Profesor) desde el BackOffice.

> Reglas: `rules/RULES-stack.md` · Skill: `skills/SKILL-nginx.md` · Tareas: `tareas/F1..F5.md`.