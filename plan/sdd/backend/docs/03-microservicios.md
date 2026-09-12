# 03 — Microservicios (los 2 propietarios)

El Backoffice (Tema 12) es **consumidor puro**: solo **2 servicios propietarios**. Cada uno con **base propia** y capas Clean Architecture. No hay FKs entre bases; las relaciones se modelan con IDs.

## Administration & Configuration Service

- Registro de parámetros **PAR-01..PAR-23** (base PRD PAR-01..18; PAR-24 asignado al Tema 01; registro genérico/extensible), versionados, hacia adelante.
- **Gestión de proveedores de LLM** (RF-IA-35): alta, sustitución, baja, auditada — exclusiva ADMIN.
- **Asignación modelo ↔ función** (RF-IA-23/24) y configuración del evaluador (RF-IA-25/28).
- **Golden set base y calibración a nivel plataforma** (RF-IA-30/31) y **detección de deriva** (RF-IA-32).
- Autoriza sus endpoints consumiendo roles del **Tema 01**.
- **Base:** `administration_db`
- **No debe:** llamar a los LLM (Tema 07 los usa), ni implementar identidad/auth/auditoría (Tema 01) ni cohorte (Tema 02).

## Reporting & Analytics Service

- **Reportes docentes** por cohorte (PROFESOR) y consolidado de plataforma (ADMIN).
- **Panel del profesor** con **indicador de alumno en riesgo**.
- **Métricas de cohorte**: satisfacción (**KPIs CSAT 5★**, encuestas agregadas/anónimas), engagement, aprobación/abandono.
- **Exportación de datos** (CSV/PDF) y **alertas configurables**.
- **Frescura ≤ 15 minutos**; **sin comparación entre docentes**.
- Consume **contratos de lectura** de los temas 02/04/05/07/08/10 → read models.
- **Base:** `reporting_db` (reconstruible por contratos de lectura (REST)).

## Consumidos (no implementados)

| Dominio | Tema | Uso del Backoffice |
|---|---|---|
| Identidad, auth, 2FA, roles, sesión, **auditoría**, retención, **API Gateway** | **T01** | Consume para autenticar/autorizar/auditar |
| Curso-cohorte, matrícula, padrón | **T02** | Consume la cohorte (`course_id`) y la pertenencia docente |
| Desafíos, teóricos, encuestas, prácticas, sandbox, evaluación LLM, banco, mercado, roadmap, social | Temas 03-11 | Solo **lectura** (02/04/05/07/08/10) |

## Tabla de bases

| Servicio | Base |
|---|---|
| Administration & Configuration | administration_db |
| Reporting & Analytics | reporting_db |

> Detalle: `backoffice_backend_requerimientos_arquitectura.md` (§8) · `TUP_PIV_BE_PROPUESTA_ARQ.pdf` (Tema 12).