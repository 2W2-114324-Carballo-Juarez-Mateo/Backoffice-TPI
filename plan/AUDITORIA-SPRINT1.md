# AUDITORIA-SPRINT1.md

> Auditoría del plan del Sprint 1 (Backoffice, Tema 12) previa a la planning.
> Referenciada desde `CAMBIOS-SPRINT1.md`. Fecha: 2026-09-18.

## Resultado

- **Versión auditada:** `plan/sprint1/tareas/` (9 devs, 192 h, 5 US, 2 datasources).
- **Versión corregida:** `plan/sprint1/tareas/tareas-sprint1.md` (10 devs, ~237 h + infra, 4 US, mono-módulo con 1 datasource + 2 esquemas).
- **Veredicto:** la versión corregida es viable y está alineada al repo oficial; la anterior quedaba obsoleta por el cambio de arquitectura y de alcance.

## Hallazgos y correcciones

| # | Hallazgo | Estado en la versión corregida |
|---|----------|-------------------------------|
| A1 | 2 datasources / 2 DBs vs repo oficial mono-módulo | Corregido: **1 datasource + 2 esquemas** (`administration`/`reporting`) |
| A2 | Mensajería: envelope propio con `role` vs DTO oficial de cátedra (5 campos + headers) | **PENDIENTE**: confirmar con Usuarios/cátedra; se mantiene envelope actual hasta confirmar |
| A3 | US-04 (Modelos IA) en Sprint 1 sin valor cierre | **Movida al Sprint 2** |
| A4 | US-08 con más topics de los acordados | **Acotada a T03 + T02** (+T08 REST) |
| A5 | Frontend fuera del sprint | **Entra con 6 tareas (FE-1..FE-6)** sobre el repo canónico **`/FE` del Demo** |
| A6 | US-03 no cubría sus 4 CA (gestión de rol) | **Agregadas tareas de rol vía T01** (dev-7) |
| A7 | DoD Nivel 1 exigía RLS en todas las historias | **RLS acotado** a historias con datos por `course_id` |
| A8 | Cobertura de capas despareja (Back 8/9, Front 4/9, Test 3/9) | **División balanceada por capas**: todos tocan back + front + test + documentación |
| A9 | Ana Paula sin tareas / fuera del reparto | **Incluida (Dev 10)** con bloque MSII: solo docs/diagramas/contratos |
| A10 | Pruebas de integración del propio autor | **Nadie testea/revisa lo suyo** (integración por otra persona) |
| A11 | Scaffolding / PR #0 sin dueño claro | **Luciano (Dev 1)**: I0a/I0b/I0c |
| A12 | Outbox migrado tarde (conflictos día 1) | **Migración outbox_message en el PR #1 (Día 2)** |
| A13 | CA faltantes: Idempotency-Key, auditoría T01, @RestControllerAdvice, headers Kafka | **Agregadas** como tareas (US-01 T12, US-01 T11, US-03 T9, US-02 T11) |
| A14 | Capacidad sin ceremonias y con días dispares | **Recalculada** con ceremonias (-8,5 h) y % de dedicación; 10 integrantes |
| A15 | `AUDITORIA-SPRINT1.md` referenciado pero inexistente | **Este documento** |

## Pendientes fuera del alcance de esta auditoría

- Reconfirmar el formato de evento (A2) con Usuarios (T01) y, si aplica, la carpeta común `com.utn.tpi.common.dto.EventoDTO`.
- Definir el deploy de staging (Workflow oficial solo hace build-and-push a GHCR).
- Confirmar DoD de reportes (RLS + view) con T10/T11.