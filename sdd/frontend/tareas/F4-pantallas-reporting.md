# Tarea F4 — Pantallas de reporting y panel (Reporting)

> **Sprint 2 · Talla L · ~5 persona-días** · consume Reporting & Analytics vía BFF + multitenancy

## 1. Objetivo
Pantallas de **reporting**: panel general, reportes docentes, métricas/CSAT, exportación y alertas — con **selector de alcance** (curso puntual o **todos/ALL** para ADMIN).

## 2. Alcance
- **In:** panel con KPIs, reportes por curso/cohorte, export (CSV/PDF), alertas, comparativas.
- **Out:** no agrega/cruza datos por su cuenta (el BFF agrega; Reporting calcula).

## 3. Requerimientos vinculados
RF-RPT-* (panel, reportes docentes, métricas), RF-CSAT (encuestas), RF-EXP (export), multitenancy (alcance curso/ALL).

## 4. Diseño técnico
- **Flujo:** pantalla → BFF `/panel` o `/reportes` → Gateway → Reporting & Analytics (read models tenant-scoped).
- **Multitenancy:** el selector de alcance define `course_id` puntual o `ALL`; el BFF/TenantContext autoriza (PROFESOR → solo su curso; ADMIN → ALL). El front nunca manda un `course_id` suelto.
- **Export:** descarga generada por Reporting (backend), no en el cliente.
- **Alertas:** listado de alertas (CSAT bajo, retención próxima) con estado visto.

## 5. Pruebas
KPIs del panel, reportes por curso, export, **alcance**: PROFESOR ve solo su curso (403 a otro); ADMIN ve todos con `ALL`. Alertas.

## 6. DoD
- [ ] Panel + reportes por alcance correcto.
- [ ] Export funcional (generado por backend).
- [ ] Prueba de aislamiento: PROFESOR → otro curso → 403; ADMIN → ALL → 200.
- [ ] Alertas listadas y marcables como vistas.

> Reglas: `rules/RULES-stack.md` · `docs/09-despliegue.md` · tareas de back `05-reportes-docentes.md`.