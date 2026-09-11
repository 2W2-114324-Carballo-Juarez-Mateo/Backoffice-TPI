# 11 — Épicas e Historias de Usuario (SDD por épica)

> Documento de diseño general por **épica** (no por tarea). Cada épica describe: **objetivo · alcance técnico · historias incluidas · contratos · dependencias**. El detalle por historia (template, CA, BDD, tareas con horas) está en el repo `plan/sprint0/uh/` y `plan/sprint0/tareas.md`.

---

## EP-01 · Parámetros Globales (administration-service · T-A)

- **Objetivo:** centralizar, versionar y gobernar los parámetros de economía y operativos (PAR-01..24), con cambios solo hacia adelante (RF-CFG-06) y propagación confiable a los consumidores.
- **Alcance técnico:** entidad `GlobalParameter` (versionada) · `Idempotency-Key` · auditoría hacia T01 · tabla `outbox_events` + publisher a Kafka (`administration.events`) · caché TTL 10 min en consumidores (Temas 03/05/08/10) con contrato publicado.
- **Historias:** US-01 (5 SP) · US-02 (5 SP).
- **Contratos:** evento `GlobalConfigurationChanged` (envelope estándar, definido en Sprint 0).
- **Dependencias:** Temas 03/05/08/10 (consumidores) · T01 (auditoría) · Kafka.

## EP-02 · Administración de la Plataforma (administration-service · T-A)

- **Objetivo:** controlar quién opera la plataforma (rol administrador), con salvaguarda cero-admin y auditoría.
- **Alcance técnico:** gestión del rol sobre identidades de T01 · protección del último admin · no auto-revocación · auditoría con motivo · aviso de administradores restantes.
- **Historias:** US-03 (5 SP).
- **Contratos:** T01 (identidad/roles) · evento de auditoría/baja.
- **Dependencias:** T01. *En coordinación: delegar la gestión a T01 (`/api/admin/accounts/*`) o mantenerla en Backoffice (a definir con cátedra/T01).*

## EP-03 · Modelos LLM y Golden Set (administration-service · T-A)

- **Objetivo:** gobernanza de los modelos de IA (evaluador): qué proveedor/modelo se usa y que esté calibrado antes de habilitarse.
- **Alcance técnico:** catálogo de proveedores/modelos (claves cifradas) · estados (PENDING → APPROVED → ACTIVE/STANDBY) · modelo único activo · ejecución del golden set (despacho a T07, el Backoffice NO invoca LLMs) · tolerancia PAR-14 · monitor de deriva + fallback + alerta.
- **Historias:** US-04 (5) · US-05 (5) · US-06 (5) · US-07 (5). **US-06 BLOQUEADO** (pendiente contrato con T07: quién ejecuta el golden set).
- **Contratos:** `ModelProviderChanged` (consumido por T07) · invocación de calibración a T07 (a coordinar) · alerta de deriva a T11.
- **Dependencias:** T07 (evaluación), T11 (notificaciones), proveedores externos, cátedra (golden set).

## EP-04 · Contratos de Lectura e Ingesta (reporting-service · T-B)

- **Objetivo:** recibir y deduplicar los datos de los 6 temas (02/04/05/07/08/10) y controlar su frescura (≤ 15 min).
- **Alcance técnico:** consumidores por tema con deduplicación por `eventId` · cola de descarte (DLT) · monitor de frescura + avisos + marcado de reportes · mapeo de contratos.
- **Historias:** US-08 (5) · US-10 (3).
- **Contratos:** topics de los 6 temas (acordados en Sprint 0) · evento de degradación de frescura a T11.
- **Dependencias:** Temas 02/04/05/07/08/10 (productores) · T11. **Habilitador del tema T-B.**

## EP-05 · Observabilidad, Reportes y Panel de Riesgo (reporting-service · T-B)

- **Objetivo:** mostrar reportes y métricas (docente por cohorte · ADMIN consolidado), con privacidad (anonimato, sin rankings) y exportación.
- **Alcance técnico:** read model por alumno/cohorte + cálculo de riesgo (ROJO/AMARILLO/VERDE) · panel docente con **RLS** (`course_id` + sentinel `ALL` para ADMIN) · indicadores con bloqueo de anonimato · umbrales + alertas · exportación asíncrona (PDF/CSV).
- **Historias:** US-09 (5, Could) · US-11 (5) · US-12 (5) · US-13 (5) · US-14 (3).
- **Contratos:** T02 (matrícula/pertenencia) · T11 (notificaciones) · RLS por `course_id` (clave `app.current_course`).
- **Dependencias:** EP-04 (datos) · T02 · T11.

---

> **Regla transversal:** multitenancy (tenant = curso-cohorte + RLS) aplicada en Reporting; ver `docs/02-arquitectura.md` y la página [Multitenancy y RLS](/backend/arquitectura/multitenancy).