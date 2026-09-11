# Plan de Tareas — Backlog general (BackOffice · Tema 12)

> Todas las tareas de las **14 historias de usuario**, agrupadas por tema y épica, listas para cargar en **Taiga**.
> **Convención:** `[G06] - [ROL] - [Descripción]` · **nombre** — qué hace (en simple). *(N SP)*.
> **Roles:** `[BACKEND]` · `[FRONTEND]` · `[TEST]` · `[DOCUMENTACION]` · `[REVISION]`.

## Estimación (Story Points · Fibonacci)

> Cada tarea se estima en **Story Points (Fibonacci): 1 · 2 · 3 · 5 · 8 · 13**. No se usan talles S/M/L/XL. El SP de cada Historia es su tamaño relativo (ver matriz en `Sprint0-Propuesta.md`).
>
> **Alcance:** las tareas de **Frontend** están en el bloque **TH-03 (futura)** y **NO computan en la capacidad del sprint de Back** (DoD: backend funcional; el front se agrega como bloque propio cuando lo definamos).

---

## TH-01 · Gobernanza y Configuración Institucional

### EP-01 · Parámetros Globales

#### US-01 · Modificación y versionado de parámetros globales (PAR-01..24)

1. **[BACKEND]** Crear el registro de parámetros con migración y **seed inicial** — Tabla `global_parameter` + entidad + **seed de PAR-01..24 con los defaults del PRD** (sin seed, la plataforma arranca vacía). *(5 SP)*
2. **[BACKEND]** Endpoints GET/PUT de parámetros — Listar y modificar con DTOs y validaciones (ADMIN escribe, PROFESOR lee). *(5 SP)*
3. **[BACKEND]** Guardar la versión y el historial — Cada cambio incrementa la versión y queda registrado quién/cuándo; rechaza fechas retroactivas (RF-CFG-06). *(5 SP)*
4. **[BACKEND]** Regla de "el cambio vale de ahora en adelante" — No retroactividad: no recalcula resultados pasados. *(2 SP)*
5. **[BACKEND]** Registrar el cambio en la auditoría de T01 — Evento de auditoría (actor, recurso, resultado) de cada cambio de parámetro (RF-AUD-03). *(5 SP)*
6. **[TEST]** Tests unitarios + integración (Testcontainers: PostgreSQL) — Versionado, idempotencia, rangos, vigencia y 403 a PROFESOR. *(5 SP)*
7. **[DOCUMENTACION]** Documentar OpenAPI y actualizar el sdd — Spec springdoc de los endpoints y diagrama de secuencia. *(2 SP)*

> **Orden:** BACKEND (1+2+3) arrancan juntos; BACKEND (4) y (5) sobre (3); TEST (6) sobre (2+3); DOCUMENTACION (7) al final. La tabla `outbox` la crea US-02.

#### US-02 · Propagación del cambio de parámetro (Outbox + Kafka + caché TTL)

> Historia 100% backend. No requiere frontend.

1. **[BACKEND]** Crear la tabla outbox y guardar el aviso en la misma transacción — `outbox_events` + escritura atómica con el cambio. *(5 SP)*
2. **[BACKEND]** Enviar los avisos a Kafka — Publisher que lee pendientes, publica en `administration.events` y marca enviado. *(5 SP)*
3. **[BACKEND]** Reintentos con backoff y cola de descarte — Si el broker no está, los avisos quedan pendientes y se reenvían sin pérdida (DLT para irrecuperables). *(5 SP)*
4. **[BACKEND]** Garantizar eventId único y versión monótona en el evento publicado — Base de la idempotencia del consumidor. *(2 SP)*
5. **[BACKEND]** Publicar el contrato del consumidor + ejemplo de referencia — Para que Temas 03/05/08/10 implementen su caché TTL 10 min (no escribimos código en sus repos). *(2 SP)*
6. **[TEST]** Test de integración del ciclo completo Outbox → Kafka → consumo (Testcontainers). *(5 SP)*
7. **[TEST]** Validar resiliencia ante caída del broker y descarte de duplicados — Reenvío al restaurar; mismo eventId no se procesa dos veces. *(5 SP)*
8. **[DOCUMENTACION]** Documentar envelope, topics y contrato del consumidor — Schema JSON + guía para los consumidores. Actualizar sdd. *(2 SP)*

> **Orden:** la tarea 5 (contrato) y la documentación (8) arrancan en paralelo; el **envelope estándar ya se define en Sprint 0** (contrato de eventos, §1 punto 4). BACKEND (1) → (2) → (3+4). TEST (6+7) sobre (1+2+3). No depende de otras US.

### EP-02 · Administración de la Plataforma

#### US-03 · Asignación y revocación del rol administrador

1. **[BACKEND]** Crear el registro de administradores — Tabla + entidad para quiénes son administradores. *(5 SP)*
2. **[BACKEND]** Puntos para asignar, listar y quitar el rol — Alta, listado y baja de administradores. *(5 SP)*
3. **[BACKEND]** Proteger al último administrador — No auto-revocación en sesión y protección del último admin. *(2 SP)*
4. **[BACKEND]** Registrar todo en la auditoría — Cada alta/baja queda anotada con su motivo. *(5 SP)*
5. **[BACKEND]** Avisar cuántos administradores quedan — Al dar de baja se publica el aviso con el número restante. *(2 SP)*
6. **[TEST]** Tests de las reglas — Autorización, auto-revocación y protección del último admin. *(5 SP)*
7. **[DOCUMENTACION]** Documentar OpenAPI y actualizar el sdd — Endpoints de gestión de administradores. *(2 SP)*

> **Nota (a coordinar):** este alcance supone que el Backoffice gestiona el rol sobre un `user_id` de T01. Existe una propuesta del equipo de delegar la gestión a T01 (`/api/admin/accounts/*`) — ver revisión de EP-02 pendiente. Orden: BACKEND (1+2) → (3+4+5); TEST (6); DOCUMENTACION (7).

### EP-03 · Modelos LLM y Golden Set

#### US-04 · Registro de proveedores y modelos de IA

1. **[BACKEND]** Crear las entidades de proveedor y modelo con cifrado de claves — Tablas + API Keys cifradas. *(5 SP)*
2. **[BACKEND]** Alta de proveedor y listado de modelos — POST y GET con estado; claves enmascaradas. *(5 SP)*
3. **[BACKEND]** Estado inicial "pendiente de revisión" — Modelo nuevo no activable. *(2 SP)*
4. **[TEST]** Tests de seguridad y bloqueo — Cifrado en BD, enmascarado en respuestas, no activar pendientes. *(5 SP)*
5. **[DOCUMENTACION]** Documentar OpenAPI y actualizar el sdd — Esquemas y máquina de estados. *(2 SP)*

> **Orden:** BACKEND (1) → (2+3); TEST (4); DOCUMENTACION (5). Independiente de US-01/02/03.

#### US-05 · Sustitución y conmutación de modelos de IA

1. **[BACKEND]** Activación con validación de estado — Solo aprobado puede activarse (si no, 409); un solo activo por función. *(5 SP)*
2. **[BACKEND]** Transición de estados con Outbox — Activo ↔ reserva + persistir `ModelProviderChanged` en la misma tx. *(5 SP)*
3. **[BACKEND]** Publicar el evento con modelo anterior y nuevo — Consumido por T04/T07. *(2 SP)*
4. **[TEST]** Tests de conmutación, rechazo y evento — 409 a no aprobado; evento con previous/new. *(5 SP)*
5. **[DOCUMENTACION]** Documentar el contrato del evento y OpenAPI — Actualizar sdd. *(2 SP)*

> **Orden:** BACKEND (1+2) → (3); TEST (4); DOCUMENTACION (5). Depende de US-04 (modelos registrados).

#### US-06 · Gestión del golden set y ejecución de revisión

1. **[BACKEND]** Crear las entidades del golden set y de calibración — Tablas de casos de referencia y de resultados. *(5 SP)*
2. **[BACKEND]** Ejecutar la revisión del modelo — Despacha los casos a T07 (el Backoffice NO invoca LLMs), recibe scores y calcula el error promedio. *(8 SP)*
3. **[BACKEND]** Consultar los resultados históricos — GET de revisiones (modelo, error, fecha, resultado). *(2 SP)*
4. **[TEST]** Tests del cálculo de error con datos sintéticos — Mocks de T07; caso sin golden set; persistencia. *(5 SP)*
5. **[DOCUMENTACION]** Documentar golden set, flujo de calibración y contrato con T07 — Actualizar sdd. *(2 SP)*

> **Orden:** BACKEND (1) y DOCUMENTACION (5) en paralelo; BACKEND (2) necesita (1) + contrato T07; BACKEND (3) y TEST (4) sobre (2). Depende de US-04.

#### US-07 · Aprobación por tolerancia (PAR-14) y fallback por deriva

1. **[BACKEND]** Regla de aprobación/rechazo por PAR-14 — Error ≤ PAR-14 → aprobado; mayor → rechazado y bloqueado. *(2 SP)*
2. **[BACKEND]** Monitor programado de deriva — Re-calibra el modelo activo periódicamente. *(5 SP)*
3. **[BACKEND]** Conmutación a respaldo y alerta — Ante deriva, pasa a reserva y emite la alerta crítica. *(5 SP)*
4. **[TEST]** Tests de aprobación, rechazo y deriva — Valores dentro/fuera de tolerancia y disparo del fallback. *(5 SP)*
5. **[DOCUMENTACION]** Documentar algoritmo, umbrales y reglas de drift — Actualizar sdd. *(2 SP)*

> **Orden:** BACKEND (1) → (2) → (3); TEST (4) sobre (1+2+3); DOCUMENTACION (5). Depende de US-06.

---

## TH-02 · Observabilidad y Soporte Académico

### EP-04 · Contratos de Lectura e Ingesta

#### US-08 · Ingesta de datos de los temas con deduplicación

> Historia 100% backend. No requiere frontend.

1. **[BACKEND]** Crear la tabla de deduplicación — `processed_events` (event_id PK). *(2 SP)*
2. **[BACKEND]** Implementar consumidores para los 6 temas — Un adapter por tema que transforma el payload en read model. *(8 SP)*
3. **[BACKEND]** Deduplicación por eventId — Si ya se procesó, se descarta. *(2 SP)*
4. **[BACKEND]** Cola de descarte para malformados — DLT sin bloquear el consumo. *(2 SP)*
5. **[TEST]** Tests de ingesta, deduplicación y DLT (Testcontainers: Kafka + PostgreSQL). *(5 SP)*
6. **[DOCUMENTACION]** Mapear contratos de lectura y esquemas JSON de los 6 temas — Entregable de coordinación con los equipos. Actualizar sdd. *(5 SP)*

> **Orden:** DOCUMENTACION (6) es la PRIMERA (acordar contratos). BACKEND (1) en paralelo; BACKEND (2) sobre (1+6); BACKEND (3+4) sobre (1). TEST (5). US-10/11/13 dependen de esta.

#### US-10 · Control de frescura de los datos y avisos

1. **[BACKEND]** Monitor programado de frescura por tema — Compara el último dato con el SLA de 15 min. *(5 SP)*
2. **[BACKEND]** Marcar reportes y endpoint de estado — `isStale=true` + GET health/freshness. *(5 SP)*
3. **[BACKEND]** Quitar el aviso al normalizar — Cuando el tema vuelve a enviar datos. *(2 SP)*
4. **[TEST]** Tests de detección de obsolescencia y normalización — 16 min sin datos → aviso; al volver, se retira. *(5 SP)*
5. **[DOCUMENTACION]** Documentar SLA de frescura en OpenAPI y actualizar el sdd. *(2 SP)*

> **Orden:** BACKEND (1+2) y DOCUMENTACION (5) en paralelo; BACKEND (3) sobre (2); TEST (4) sobre (1+2+3). Depende de US-08.

### EP-05 · Observabilidad, Reportes y Panel de Riesgo

#### US-09 · Exportación de reportes

> **Prioridad Could:** implementar SOLO si las Must y Should están completas.

1. **[BACKEND]** Solicitud de exportación asíncrona — POST → 202 con `exportId`; encola en segundo plano. *(5 SP)*
2. **[BACKEND]** Generador de archivos en streaming (PDF/CSV) — Sin cargar todo en memoria. *(8 SP)*
3. **[BACKEND]** Enlace temporal con vencimiento y alcance por rol — Token con TTL (410 si expira); PROFESOR solo sus comisiones. *(5 SP)*
4. **[TEST]** Tests de generación, alcance y vencimiento. *(5 SP)*
5. **[DOCUMENTACION]** Documentar el flujo asíncrono en OpenAPI y actualizar el sdd. *(2 SP)*

> **Orden:** BACKEND (1) y DOCUMENTACION (5) en paralelo; BACKEND (2) sobre (1); BACKEND (3) sobre (2); TEST (4). Depende de US-08.

#### US-11 · Read model y cálculo de riesgo por cohorte

> Historia 100% backend. No requiere frontend (se expone en US-12).

1. **[BACKEND]** Crear el read model analítico por cohorte — Tabla con `course_id`, actividad, aprobación y `risk_level`; índices. *(5 SP)*
2. **[BACKEND]** Algoritmo de clasificación de riesgo — ROJO / AMARILLO / VERDE según inactividad y aprobación. *(5 SP)*
3. **[BACKEND]** Job periódico de recálculo — Foto analítica que actualiza `risk_level`. *(5 SP)*
4. **[TEST]** Tests de partición de equivalencia del algoritmo — Valores límite (13 vs 14 días, 39% vs 40%). *(5 SP)*
5. **[DOCUMENTACION]** Documentar reglas de cálculo y esquema del read model — Actualizar sdd. *(2 SP)*

> **Orden:** BACKEND (1) y DOCUMENTACION (5) en paralelo; BACKEND (2) sobre (1); TEST (4) con la interfaz del algoritmo; BACKEND (3) sobre (1+2). Depende de US-08.

#### US-12 · Panel del docente con RLS y alerta de riesgo

1. **[BACKEND]** Endpoint del panel docente con validación de matrícula — Pertenencia vía T02; si no → 403. *(5 SP)*
2. **[BACKEND]** Migración RLS por course_id + sentinel ALL para ADMIN — `ENABLE ROW LEVEL SECURITY` + políticas + alcance global (solo ADMIN, auditado). *(5 SP)*
3. **[BACKEND]** Emitir notificación ante riesgo alto — Evento al pasar a ROJO, consumido por T11. *(2 SP)*
4. **[TEST]** Tests de RLS: docente A solo ve cohorte A — 200/403 + intento ALL → 403 + ADMIN global → 200. *(5 SP)*
5. **[TEST]** Validar regla anti-comparación y emisión de alerta. *(2 SP)*
6. **[DOCUMENTACION]** Especificar endpoints del panel, RLS y contrato de alertas — Actualizar sdd. *(2 SP)*

> **Orden:** BACKEND (1+2) en paralelo; BACKEND (3) sobre (1); TEST (4+5) sobre (1+2+3); DOCUMENTACION (6). Depende de US-11.

#### US-13 · Indicadores consolidados con bloqueo de anonimato

1. **[BACKEND]** Servicio de agregación de indicadores — CSAT, engagement, aprobación/abandono desde los read models. *(5 SP)*
2. **[BACKEND]** Protección de anonimato por umbral — Menos de N respuestas → "muestra insuficiente". *(2 SP)*
3. **[BACKEND]** Endpoint exclusivo ADMIN con regla anti-comparación — Solo ADMIN; sin ranking de docentes. *(5 SP)*
4. **[TEST]** Tests de anonimato y autorización — Ocultamiento con N bajo; no-ADMIN → 403. *(5 SP)*
5. **[DOCUMENTACION]** Documentar políticas de privacidad y fórmulas — Actualizar sdd y OpenAPI. *(2 SP)*

> **Orden:** BACKEND (1+2) y DOCUMENTACION (5) en paralelo; BACKEND (3) sobre (1+2); TEST (4). Depende de US-08.

#### US-14 · Umbrales de aviso y acceso al tablero

1. **[BACKEND]** Modelo de umbrales y CRUD exclusivo ADMIN — Tabla `alert_thresholds`; solo ADMIN (403 a otros). *(5 SP)*
2. **[BACKEND]** Evaluador periódico contra umbrales y alertas — Si un indicador baja del umbral, se emite el aviso. *(5 SP)*
3. **[TEST]** Tests de evaluación de umbrales y autorización — Configurar, baja → alerta, no-ADMIN → 403. *(5 SP)*
4. **[DOCUMENTACION]** Documentar catálogo de umbrales y eventos de alerta — Actualizar sdd. *(2 SP)*

> **Orden:** BACKEND (1) y DOCUMENTACION (4) en paralelo; BACKEND (2) sobre (1) + indicadores de US-13; TEST (3). Depende de US-13.

---

## TH-03 · Frontend (futura) — NO computa en la capacidad del sprint de Back

> Estas tareas pertenecen a la **materia Front** (bloque propio a definir). Se listan acá para no perderlas, pero **no se suman a la capacidad del sprint de Back**.

#### US-01
- **[FRONTEND]** Pantalla de catálogo y formulario reactivo de edición de parámetros. *(5 SP)*

#### US-03
- **[FRONTEND]** Pantalla de gestión de administradores. *(5 SP)*

#### US-04
- **[FRONTEND]** Pantalla de registro de proveedores y modelos. *(5 SP)*

#### US-05
- **[FRONTEND]** Acción de activar modelo en el catálogo. *(2 SP)*

#### US-06
- **[FRONTEND]** Pantalla de resultados de revisión. *(5 SP)*

#### US-07
- **[FRONTEND]** Estado del modelo y aviso de deriva en la pantalla. *(2 SP)*

#### US-09
- **[FRONTEND]** Botón de exportación y aviso de descarga. *(5 SP)*

#### US-10
- **[FRONTEND]** Insignia de datos desactualizados en el panel. *(2 SP)*

#### US-12
- **[FRONTEND]** Panel docente con semáforo de riesgo. *(5 SP)*

#### US-13
- **[FRONTEND]** Tablero de indicadores. *(5 SP)*

#### US-14
- **[FRONTEND]** Configuración de umbrales y lista de alertas. *(2 SP)*

---

> **Totales (referencia):** 14 historias · tareas de **BACKEND/TEST/DOCUMENTACION** listadas por UH + **11 tareas FRONTEND** en TH-03 (fuera de la capacidad de Back). Estimación por **Story Points (Fibonacci)**.