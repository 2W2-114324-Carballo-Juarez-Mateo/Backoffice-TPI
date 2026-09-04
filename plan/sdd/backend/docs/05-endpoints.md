# 05 — Endpoints y autorización

Convención REST, APIs versionadas (`/api/v1/...`), documentadas con springdoc/OpenAPI. La autorización se valida **siempre** en el microservicio; el gateway (Tema 01) valida el token y propaga contexto (*validar ≠ autorizar*).

> **Auth y gestión de cuentas ADMIN pertenecen al Tema 01** (`/api/auth/*`, `/api/admin/accounts/*`): el Backoffice los **consume** a través del gateway, no los implementa.

## Endpoints propios

**Administration & Configuration**

```http
GET  /api/administration/parameters
GET  /api/administration/parameters/{key}
PUT  /api/administration/parameters/{key}

GET    /api/administration/model-providers
POST   /api/administration/model-providers
PUT    /api/administration/model-providers/{id}
DELETE /api/administration/model-providers/{id}

GET  /api/administration/model-functions
PUT  /api/administration/model-functions/{function}

POST /api/administration/evaluator/activate
GET  /api/administration/evaluator/calibration
```

**Reporting & Analytics (reportes, métricas, export, alertas)**

```http
GET /api/reports/platform
GET /api/reports/courses/{courseId}
GET /api/reports/courses/{courseId}/metrics
GET /api/reports/courses/{courseId}/teacher         ← panel del profesor
GET /api/reports/courses/{courseId}/teacher/risk    ← alumno en riesgo
GET /api/export/courses/{courseId}                  ← CSV/PDF
GET /api/export/platform
GET /api/alerts                                     ← alertas configurables
```

**Auditoría — consumida del Tema 01**

```http
GET /api/audit        ← contrato de lectura con el Tema 01
GET /api/audit/{id}
```

## Matriz endpoint → rol → alcance (resumen)

| Endpoint | Roles | Alcance |
|---|---|---|
| `/api/auth/*`, `/api/admin/accounts*` | Tema 01 | consumidos, no implementados |
| `/api/administration/parameters/{key}` PUT | ADMIN | exclusivo ADMIN |
| `/api/administration/model-providers*` | ADMIN | exclusivo ADMIN (RF-IA-35) |
| `/api/administration/model-functions*` | ADMIN | exclusivo ADMIN |
| `/api/administration/evaluator/*` | ADMIN | exclusivo ADMIN |
| `/api/audit` | ADMIN | global (lectura T01) |
| `/api/reports/platform` | ADMIN | global |
| `/api/reports/courses/{courseId}*` | ADMIN, PROFESOR | PROFESOR: solo su cohorte (matrícula T02) |
| `/api/reports/courses/{courseId}/teacher*` | PROFESOR, ADMIN | **sin comparación entre docentes** |
| `/api/export/*` | ADMIN / PROFESOR | según recurso |
| `/api/alerts` | ADMIN, PROFESOR | según recurso |

## Manejo de errores

Formato uniforme:

```json
{ "code": "PARAMETER_FORBIDDEN_FOR_ROLE", "message": "...", "correlationId": "..." }
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