# 05 — Endpoints y autorización

Convención REST, APIs versionadas (`/api/v1/...`), documentadas con springdoc/OpenAPI. La autorización se valida **siempre** en el microservicio (validar ≠ autorizar): el gateway (Tema 01) valida el token y propaga contexto (`X-User-Id`, `X-User-Roles`, `traceparent`, `X-Request-Id`); cada servicio autoriza **localmente** con `@PreAuthorize` sobre el rol propagado.

> **Auth y gestión de cuentas ADMIN pertenecen al Tema 01** (`/api/auth/*`, `/api/admin/accounts/*`): el Backoffice los **consume** a través del gateway, no los implementa.

## Convención de rutas (contrato con T01)

Toda API pública vive bajo `/api/{servicio}/**` (prefijo definido en el gateway; sin él → 404). Nuestros endpoints propios están bajo `/api/administration/**` o `/api/reports/**`. Los servicios de otros temas se consumen bajo su prefijo (ej. T01 = `/api/users/**`).

## Endpoints propios

**Administration & Configuration** → `/api/administration/**`

```http
GET    /api/administration/parameters
GET    /api/administration/parameters/{key}
PUT    /api/administration/parameters/{key}

GET    /api/administration/model-providers
POST   /api/administration/model-providers
PUT    /api/administration/model-providers/{id}
DELETE /api/administration/model-providers/{id}

GET  /api/administration/model-functions
PUT  /api/administration/model-functions/{function}

POST /api/administration/evaluator/activate
GET  /api/administration/evaluator/calibration
```

**Reporting & Analytics (reportes, métricas, export, alertas)** → `/api/reports/**`

```http
GET /api/reports/platform
GET /api/reports/courses/{courseId}
GET /api/reports/courses/{courseId}/metrics
GET /api/reports/courses/{courseId}/teacher         ← panel del profesor
GET /api/reports/courses/{courseId}/teacher/risk    ← alumno en riesgo
GET /api/reports/export/courses/{courseId}          ← CSV/PDF
GET /api/reports/export/platform
GET /api/reports/alerts                             ← alertas configurables
```

## Endpoints consumidos del Tema 01

```http
GET /api/users/audit        ← contrato de lectura de auditoría (ADMIN, paginado)
GET /api/users/audit/{id}
GET /api/users/retention/policy          ← opcional (postergado)
GET /api/users/retention/records         ← opcional (postergado)
```

## Matriz endpoint → rol → alcance (resumen)

| Endpoint | Roles | Alcance |
|---|---|---|
| `/api/auth/*`, `/api/admin/accounts*` | Tema 01 | consumidos, no implementados |
| `/api/administration/parameters` GET | ADMIN, PROFESOR | PROFESOR: solo lectura |
| `/api/administration/parameters/{key}` PUT | ADMIN | exclusivo ADMIN |
| `/api/administration/model-providers*` | ADMIN | exclusivo ADMIN (RF-IA-35) |
| `/api/administration/model-functions*` | ADMIN | exclusivo ADMIN |
| `/api/administration/evaluator/*` | ADMIN | exclusivo ADMIN |
| `/api/users/audit` | ADMIN | global (lectura T01) |
| `/api/reports/platform` | ADMIN | global (alcance `ALL` server-side) |
| `/api/reports/courses/{courseId}*` | ADMIN, PROFESOR | PROFESOR: solo su cohorte (matrícula T02) |
| `/api/reports/courses/{courseId}/teacher*` | PROFESOR, ADMIN | **sin comparación entre docentes** |
| `/api/reports/export/*` | ADMIN / PROFESOR | según recurso |
| `/api/reports/alerts` | ADMIN, PROFESOR | según recurso |

> **Roles reales (contrato T01):** `ADMIN`, `PROFESOR`, `ALUMNO` (+ `MS` solo service-to-service). **No existe `AUDITOR`**: la lectura de auditoría es `ADMIN`.

## Manejo de errores

Formato uniforme:

```json
{ "code": "PARAMETER_FORBIDDEN_FOR_ROLE", "message": "...", "requestId": "..." }
```

| Código HTTP | Uso |
|---|---:|
| 200/201/204 | Éxito |
| 400 / 401 / 403 / 404 | Datos / auth / permisos / no existe |
| 409 | Regla de negocio |
| **429** | **Rate limiting (con `Retry-After`)** |
| 422 | Validación semántica |
| 500 / 503 | Error / dependencia no disponible |

Códigos de negocio destacados: `PARAMETER_FORBIDDEN_FOR_ROLE`, `MODEL_PROVIDER_FORBIDDEN_FOR_ROLE`, `MODEL_EVALUATOR_CALIBRATION_REQUIRED`, `COURSE_NOT_ACCESSIBLE` (cross-team), `REPORT_ANONYMITY_VIOLATION`, `RATE_LIMIT_EXCEEDED`.

> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§28-§29).