# Plan de Tareas — Backlog general (BackOffice · Tema 12)

> Todas las tareas de las **14 historias de usuario**, agrupadas por tema y épica, listas para cargar en **Taiga**.
> **Convención:** `[G06] - [ROL] - [Descripción]` · **nombre** — qué hace (en simple). *(horas)*.
> **Roles:** `[BACKEND]` · `[FRONTEND]` · `[TEST]` · `[DOCUMENTACION]` · `[REVISION]`.

## Estimación

- **Historia = Story Points (Fibonacci): 1 · 2 · 3 · 5 · 8 · 13** — tamaño relativo (ver matriz en `Sprint0-Propuesta.md`).
- **Tarea = horas** — la capacidad del sprint se calcula en horas (Excel).
- **Alcance:** las tareas de **Frontend** están en el bloque **TH-03 · Experiencia de Usuario (futura)** y **NO computan en la capacidad del sprint de Back** (DoD: backend funcional).

---

## TH-01 · Gobernanza y Configuración Institucional

### EP-01 · Parámetros Globales

#### US-01 · Modificación y versionado de parámetros globales (5 SP) · **Back ≈ 55 h**

1. **[G06] - [BACKEND] - Crear el registro de parámetros con migración y seed inicial** — Tabla `global_parameter` + entidad + **seed de PAR-01..18 (defaults del PRD)**. PAR-19..24 solo tras validar con la cátedra. *(8 h)*
2. **[G06] - [BACKEND] - Endpoints GET/PUT de parámetros con autorización** — Listar y modificar con DTOs y validaciones (ADMIN escribe, PROFESOR lee). *(8 h)*
3. **[G06] - [BACKEND] - Guardar la versión y el historial** — Cada cambio incrementa la versión y queda registrado quién/cuándo. *(8 h)*
4. **[G06] - [BACKEND] - Regla de "el cambio vale de ahora en adelante" (RF-CFG-06)** — No retroactividad: no recalcula resultados pasados. *(4 h)*
5. **[G06] - [BACKEND] - Implementar Idempotency-Key en el PUT** — Si llega dos veces el mismo cambio, se aplica una sola vez. *(4 h)*
6. **[G06] - [BACKEND] - Registrar el cambio en la auditoría de T01** — Evento de auditoría (actor, recurso, resultado) de cada cambio (RF-AUD-03). *(8 h)*
7. **[G06] - [TEST] - Tests unitarios + integración (Testcontainers: PostgreSQL)** — Versionado, idempotencia, rangos, vigencia y 403 a PROFESOR. *(8 h)*
8. **[G06] - [DOCUMENTACION] - Documentar OpenAPI y actualizar el sdd** — Spec springdoc de los endpoints. *(4 h)*
9. **[G06] - [REVISION] - Peer review de PR y validación de DoD Nivel 1** — Clean Architecture, sin secretos, CI verde. *(3 h)*

> **Orden:** BACKEND (1+2+3) en paralelo → (4+5+6); TEST (7) sobre (2+5); DOCUMENTACION (8); REVISION (9). La tabla `outbox` la crea US-02.

#### US-02 · Propagación del cambio de parámetro (5 SP) · **Back ≈ 55 h**

> Historia 100% backend. No requiere frontend.

1. **[G06] - [BACKEND] - Crear la tabla outbox y guardar el aviso en la misma transacción** — `outbox_events` + escritura atómica con el cambio. *(8 h)*
2. **[G06] - [BACKEND] - Enviar los avisos a Kafka** — Publisher que lee pendientes, publica en `administration.events` y marca enviado. *(8 h)*
3. **[G06] - [BACKEND] - Reintentos con backoff y cola de descarte (DLT)** — Sin pérdida si el broker no está; irrecuperables a DLT. *(8 h)*
4. **[G06] - [BACKEND] - Garantizar eventId único y versión monótona en el evento publicado** — Base de la idempotencia del consumidor. *(4 h)*
5. **[G06] - [BACKEND] - Publicar el contrato del consumidor + ejemplo de referencia** — Para que Temas 03/05/08/10 implementen su caché TTL 10 min (no escribimos en sus repos). *(4 h)*
6. **[G06] - [TEST] - Test de integración del ciclo completo Outbox → Kafka → consumo** (Testcontainers). *(8 h)*
7. **[G06] - [TEST] - Validar resiliencia ante caída del broker y descarte de duplicados** — Reenvío al restaurar; mismo eventId no se procesa dos veces. *(8 h)*
8. **[G06] - [DOCUMENTACION] - Documentar envelope, topics y contrato del consumidor** — Schema JSON + guía. Actualizar sdd. *(4 h)*
9. **[G06] - [REVISION] - Peer review de concurrencia y transaccionalidad** — Atomicidad de tx y ausencia de race conditions. *(3 h)*

> **Orden:** el **envelope estándar se define en Sprint 0** (contrato de eventos, §1 punto 4). BACKEND (1) → (2) → (3+4+5); TEST (6+7); DOCUMENTACION (8); REVISION (9).

### EP-02 · Administración de la Plataforma

#### US-03 · Asignación y revocación del rol administrador (5 SP) · **Back ≈ 47 h**

1. **[G06] - [BACKEND] - Crear el registro de administradores** — Tabla + entidad para quiénes son administradores. *(8 h)*
2. **[G06] - [BACKEND] - Puntos para asignar, listar y quitar el rol** — Alta, listado y baja de administradores. *(8 h)*
3. **[G06] - [BACKEND] - Proteger al último administrador** — No auto-revocación en sesión y protección del último admin. *(4 h)*
4. **[G06] - [BACKEND] - Registrar todo en la auditoría** — Cada alta/baja queda anotada con su motivo. *(8 h)*
5. **[G06] - [BACKEND] - Avisar cuántos administradores quedan** — Al dar de baja se publica el aviso con el número restante. *(4 h)*
6. **[G06] - [TEST] - Tests de las reglas** — Autorización, auto-revocación y protección del último admin. *(8 h)*
7. **[G06] - [DOCUMENTACION] - Documentar OpenAPI y actualizar el sdd** *(4 h)*
8. **[G06] - [REVISION] - Peer review de fronteras y seguridad** *(3 h)*

> **Nota (a coordinar):** este alcance supone que el Backoffice gestiona el rol sobre un `user_id` de T01. Existe la propuesta del equipo de delegar la gestión a T01 (`/api/admin/accounts/*`) — ver revisión de EP-02 pendiente.

### EP-03 · Modelos LLM y Golden Set

#### US-04 · Registro de proveedores y modelos de IA (5 SP) · **Back ≈ 35 h**

1. **[G06] - [BACKEND] - Crear las entidades de proveedor y modelo con cifrado de claves** — Tablas + API Keys cifradas. *(8 h)*
2. **[G06] - [BACKEND] - Alta de proveedor y listado de modelos** — POST y GET con estado; claves enmascaradas. *(8 h)*
3. **[G06] - [BACKEND] - Estado inicial "pendiente de revisión"** — Modelo nuevo no activable. *(4 h)*
4. **[G06] - [TEST] - Tests de seguridad y bloqueo** — Cifrado en BD, enmascarado en respuestas, no activar pendientes. *(8 h)*
5. **[G06] - [DOCUMENTACION] - Documentar OpenAPI y actualizar el sdd** — Esquemas y máquina de estados. *(4 h)*
6. **[G06] - [REVISION] - Peer review de seguridad de credenciales** — Sin API Keys en logs/respuestas/repo. *(3 h)*

#### US-05 · Sustitución y conmutación de modelos de IA (5 SP) · **Back ≈ 35 h**

1. **[G06] - [BACKEND] - Activación con validación de estado** — Solo aprobado puede activarse (si no, 409); un solo activo por función. *(8 h)*
2. **[G06] - [BACKEND] - Transición de estados con Outbox** — Activo ↔ reserva + persistir `ModelProviderChanged` en la misma tx. *(8 h)*
3. **[G06] - [BACKEND] - Publicar el evento con modelo anterior y nuevo** — Consumido por **T07**. *(4 h)*
4. **[G06] - [TEST] - Tests de conmutación, rechazo y evento** — 409 a no aprobado; evento con previous/new. *(8 h)*
5. **[G06] - [DOCUMENTACION] - Documentar el contrato del evento y OpenAPI** — Actualizar sdd. *(4 h)*
6. **[G06] - [REVISION] - Peer review de unicidad y atomicidad** *(3 h)*

#### US-06 · Gestión del golden set y ejecución de revisión (5 SP) · **Back ≈ 39 h** · ⚠️ **BLOQUEADO**

> ⚠️ **BLOQUEADO — pendiente de contrato con T07:** quién ejecuta el golden set (la arquitectura define a T07 como consumidor; no se confirmó que exponga un endpoint de calibración). **No cargar en Taiga hasta coordinar con T07/cátedra.**

1. **[G06] - [BACKEND] - Crear las entidades del golden set y de calibración** — Tablas de casos de referencia y de resultados. *(8 h)*
2. **[G06] - [BACKEND] - Ejecutar la revisión despachando los casos a T07** — Envía casos a T07 (el Backoffice NO invoca LLMs), recibe scores y calcula el error promedio. *(12 h)*
3. **[G06] - [BACKEND] - Consultar los resultados históricos** — GET de revisiones (modelo, error, fecha, resultado). *(4 h)*
4. **[G06] - [TEST] - Tests del cálculo de error con datos sintéticos** — Mocks de T07; caso sin golden set. *(8 h)*
5. **[G06] - [DOCUMENTACION] - Documentar golden set, flujo de calibración y contrato con T07** *(4 h)*
6. **[G06] - [REVISION] - Peer review de algoritmos y precisión** *(3 h)*

#### US-07 · Aprobación por tolerancia (PAR-14) y fallback por deriva (5 SP) · **Back ≈ 35 h**

1. **[G06] - [BACKEND] - Regla de aprobación/rechazo por PAR-14** — Error ≤ PAR-14 → aprobado; mayor → rechazado y bloqueado. *(4 h)*
2. **[G06] - [BACKEND] - Monitor programado de deriva** — Re-calibra el modelo activo periódicamente. *(8 h)*
3. **[G06] - [BACKEND] - Conmutación a respaldo y alerta** — Ante deriva, pasa a reserva y emite la alerta crítica. *(8 h)*
4. **[G06] - [TEST] - Tests de aprobación, rechazo y deriva** — Valores dentro/fuera de tolerancia y disparo del fallback. *(8 h)*
5. **[G06] - [DOCUMENTACION] - Documentar algoritmo, umbrales y reglas de drift** *(4 h)*
6. **[G06] - [REVISION] - Peer review del monitor y la conmutación** *(3 h)*

---

## TH-02 · Observabilidad y Soporte Académico

### EP-04 · Contratos de Lectura e Ingesta

#### US-08 · Ingesta de datos de los temas con deduplicación (5 SP) · **Back ≈ 43 h**

> Historia 100% backend. No requiere frontend.

1. **[G06] - [BACKEND] - Crear la tabla de deduplicación** — `processed_events` (event_id PK). *(4 h)*
2. **[G06] - [BACKEND] - Implementar consumidores para los 6 temas** — Un adapter por tema que transforma el payload en read model. *(12 h)*
3. **[G06] - [BACKEND] - Deduplicación por eventId** — Si ya se procesó, se descarta. *(4 h)*
4. **[G06] - [BACKEND] - Cola de descarte para malformados (DLT)** — Sin bloquear el consumo. *(4 h)*
5. **[G06] - [TEST] - Tests de ingesta, deduplicación y DLT** (Testcontainers: Kafka + PostgreSQL). *(8 h)*
6. **[G06] - [DOCUMENTACION] - Mapear contratos de lectura y esquemas JSON de los 6 temas** — Entregable de coordinación. *(8 h)*
7. **[G06] - [REVISION] - Peer review de consumidores y offsets** *(3 h)*

> **Orden:** DOCUMENTACION (6) es la PRIMERA (acordar contratos). US-10/11/13 dependen de esta.

#### US-10 · Control de frescura de los datos y avisos (3 SP) · **Back ≈ 39 h**

1. **[G06] - [BACKEND] - Monitor programado de frescura por tema** — Compara el último dato con el SLA de 15 min. *(8 h)*
2. **[G06] - [BACKEND] - Marcar reportes y endpoint de estado** — `isStale=true` + GET health/freshness. *(8 h)*
3. **[G06] - [BACKEND] - Quitar el aviso al normalizar** — Cuando el tema vuelve a enviar datos. *(4 h)*
4. **[G06] - [BACKEND] - Emitir el aviso al sistema de notificaciones** — Al detectar obsolescencia se notifica (T11). *(4 h)*
5. **[G06] - [TEST] - Tests de detección de obsolescencia y normalización** — 16 min sin datos → aviso; al volver, se retira. *(8 h)*
6. **[G06] - [DOCUMENTACION] - Documentar SLA de frescura en OpenAPI y actualizar el sdd** *(4 h)*
7. **[G06] - [REVISION] - Peer review del monitor** *(3 h)*

### EP-05 · Observabilidad, Reportes y Panel de Riesgo

#### US-09 · Exportación de reportes (5 SP) · **Back ≈ 47 h** · Could

1. **[G06] - [BACKEND] - Solicitud de exportación asíncrona** — POST → 202 con `exportId`; encola en segundo plano. *(8 h)*
2. **[G06] - [BACKEND] - Generador de archivos en streaming (PDF/CSV)** — Sin cargar todo en memoria. *(12 h)*
3. **[G06] - [BACKEND] - Enlace temporal con vencimiento y alcance por rol** — Token con TTL (410 si expira); PROFESOR solo sus comisiones. *(8 h)*
4. **[G06] - [BACKEND] - Avisar que el archivo está listo** — Notificación + URL temporal. *(4 h)*
5. **[G06] - [TEST] - Tests de generación, alcance y vencimiento** *(8 h)*
6. **[G06] - [DOCUMENTACION] - Documentar el flujo asíncrono en OpenAPI y actualizar el sdd** *(4 h)*
7. **[G06] - [REVISION] - Peer review de streams/memoria** *(3 h)*

#### US-11 · Read model y cálculo de riesgo por cohorte (5 SP) · **Back ≈ 39 h**

> Historia 100% backend (resultado se expone en US-12). El riesgo se calcula **por alumno** (agrupado por curso-cohorte).

1. **[G06] - [BACKEND] - Crear el read model analítico por alumno** — Tabla con `course_id` + `student_id`, actividad, aprobación y `risk_level`; índices. *(8 h)*
2. **[G06] - [BACKEND] - Algoritmo de clasificación de riesgo** — ROJO (>10 días o reprobación >60% o vidas agotadas) · AMARILLO (5-10 días o 40-60%) · VERDE (≥70%). *(8 h)*
3. **[G06] - [BACKEND] - Job periódico de recálculo** — Foto analítica que actualiza `risk_level`. *(8 h)*
4. **[G06] - [TEST] - Tests de partición de equivalencia del algoritmo** — Valores límite **10/11 días y 60/61%**. *(8 h)*
5. **[G06] - [DOCUMENTACION] - Documentar reglas de cálculo y esquema del read model** *(4 h)*
6. **[G06] - [REVISION] - Peer review de modelado e índices** *(3 h)*

#### US-12 · Panel del docente con RLS y alerta de riesgo (5 SP) · **Back ≈ 43 h**

1. **[G06] - [BACKEND] - Endpoint del panel docente con validación de matrícula** — Pertenencia vía T02; si no → 403. *(8 h)*
2. **[G06] - [BACKEND] - Migración RLS por course_id + sentinel ALL para ADMIN** — `ENABLE ROW LEVEL SECURITY` + políticas + alcance global (solo ADMIN, auditado). *(8 h)*
3. **[G06] - [BACKEND] - Emitir notificación ante riesgo alto** — Evento al pasar a ROJO, consumido por T11. *(4 h)*
4. **[G06] - [BACKEND] - Regla anti-comparación (RF-RPT-07)** — La vista no expone métricas de otros docentes. *(4 h)*
5. **[G06] - [TEST] - Tests de RLS: docente A solo ve cohorte A** — 200/403 + intento ALL → 403 + ADMIN global → 200. *(8 h)*
6. **[G06] - [TEST] - Validar regla anti-comparación y emisión de alerta** *(4 h)*
7. **[G06] - [DOCUMENTACION] - Especificar endpoints del panel, RLS y contrato de alertas** *(4 h)*
8. **[G06] - [REVISION] - Peer review de seguridad RLS (punto crítico)** *(3 h)*

#### US-13 · Indicadores consolidados con bloqueo de anonimato (5 SP) · **Back ≈ 35 h**

1. **[G06] - [BACKEND] - Servicio de agregación de indicadores** — CSAT, engagement, aprobación/abandono desde los read models. *(8 h)*
2. **[G06] - [BACKEND] - Protección de anonimato por umbral** — Menos de N respuestas → "muestra insuficiente". *(4 h)*
3. **[G06] - [BACKEND] - Endpoint exclusivo ADMIN con regla anti-comparación** — Solo ADMIN; sin ranking de docentes. *(8 h)*
4. **[G06] - [TEST] - Tests de anonimato y autorización** — Ocultamiento con N bajo; no-ADMIN → 403. *(8 h)*
5. **[G06] - [DOCUMENTACION] - Documentar políticas de privacidad y fórmulas** *(4 h)*
6. **[G06] - [REVISION] - Peer review de privacidad** *(3 h)*

#### US-14 · Umbrales de aviso y acceso al tablero (3 SP) · **Back ≈ 31 h**

1. **[G06] - [BACKEND] - Modelo de umbrales y CRUD exclusivo ADMIN** — Tabla `alert_thresholds`; solo ADMIN (403 a otros). *(8 h)*
2. **[G06] - [BACKEND] - Evaluador periódico contra umbrales y alertas** — Si un indicador baja del umbral, se emite el aviso. *(8 h)*
3. **[G06] - [TEST] - Tests de evaluación de umbrales y autorización** — Configurar, baja → alerta, no-ADMIN → 403. *(8 h)*
4. **[G06] - [DOCUMENTACION] - Documentar catálogo de umbrales y eventos de alerta** *(4 h)*
5. **[G06] - [REVISION] - Peer review final y cierre en Taiga** *(3 h)*

---

## TH-03 · Experiencia de Usuario (Frontend, futura) — NO computa en la capacidad del sprint de Back

> Estas tareas pertenecen a la **materia Front** (bloque propio a definir). Se listan acá para no perderlas; **no se suman a la capacidad del sprint de Back**.

- **[FRONTEND]** Pantalla de catálogo y formulario de edición de parámetros (US-01) · Pantalla de gestión de administradores (US-03) · Pantalla de registro de proveedores/modelos (US-04) · Acción de activar modelo (US-05) · Pantalla de resultados de revisión (US-06) · Estado del modelo y aviso de deriva (US-07) · Botón de exportación y aviso de descarga (US-09) · Insignia de datos desactualizados (US-10) · Panel docente con semáforo (US-12) · Tablero de indicadores (US-13) · Configuración de umbrales y alertas (US-14).

---

> **Totales de Back (referencia):** **≈ 578 h** en tareas BACKEND/TEST/DOCUMENTACION/REVISION de las 14 UH (+ 11 tareas FRONTEND en TH-03, fuera de capacidad). La capacidad real la define el Excel del equipo.