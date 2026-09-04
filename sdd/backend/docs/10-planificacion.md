# 10 — Planificación y dimensionamiento

Tareas asignadas (Tema 12) según `TUP_PIV_BE_PROPUESTA_ARQ.pdf`, en las 3 columnas del documento (MoSCoW) y dimensionadas en **talles T-shirt (S/M/L)**.

## 🟢 Must — Pedido para empezar (sprint 1)

| Ítem | RF | Subtareas | Talla | Dependencia |
|---|---|---|---|---|
| Administración de plataforma | RF-CFG-01/05 | Operativa de ADMIN sobre config/proveedores · consumo de auth/roles (T01) · permisos de endpoints | M | T01 |
| Registro de parámetros PAR-01..24 | RF-CFG-04/06 | CRUD `GlobalParameter` · versionado · hacia adelante · evento `GlobalConfigurationChanged` | M | Temas 03/05/08/10 |
| Gestión del proveedor LLM (exclusiva ADMIN) | RF-IA-ADM-01..07 | CRUD proveedores · modelo↔función · evaluador único · golden set + calibración · deriva | L | T07 consume |
| Contratos de lectura con los 6 temas | RF-RPT-10 | Acordar contratos (02/04/05/07/08/10) · suscripción a eventos · adapters · read models | L | Temas 02/04/05/07/08/10 |
| Reportes docentes | RF-RPT-01 | Read models por cohorte · endpoints de reporte · autorización por matrícula (T02) | M | Contratos |

## 🟡 Should — Para más adelante

| Ítem | RF | Subtareas | Talla | Dependencia |
|---|---|---|---|---|
| Panel del profesor (alumno en riesgo) | RF-RPT-03 | `AtRiskStudent` · indicador · endpoint | M | Lecturas T04/05/08/10 |
| Frescura ≤ 15 min | RF-RPT-06 | SLA de frescura · monitoreo de lag | S-M | Contratos |
| KPIs CSAT 5★ | RF-RPT-02 | Agregados anónimos · KPI por cohorte | S-M | T04 |
| Alertas configurables | RF-RPT-05 | Reglas configurables · `/api/alerts` | S-M | Lecturas |
| Sin comparación entre docentes | RF-RPT-07 | Scope no cross-docente · tests | S | — |

## 🔵 Could — Podría ser

| Ítem | RF | Subtareas | Talla | Dependencia |
|---|---|---|---|---|
| Exportación de datos | RF-RPT-04 | CSV/PDF · `/api/export/*` | M | Read models |

> Criterio del documento: "Pedido para empezar" = núcleo + lo que otros equipos necesitan (los **contratos de lectura** son la dependencia crítica del sprint 1: sin ellos no hay nada demostrable). "Para más adelante" = se diseña ahora y se implementa después. "Podría ser" = un extra a medias vale menos que un núcleo terminado.
> Referencia: `backoffice_backend_requerimientos_arquitectura.md` §40bis · sitio `msii/planificacion`.