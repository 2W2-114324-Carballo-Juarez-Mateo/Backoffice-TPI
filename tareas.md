# Plan de Tareas — Backlog general (BackOffice · Tema 12)

> Todas las tareas de las 14 historias de usuario, agrupadas por épica, con la convención **`[G06] - [ROL] - [Descripción]`** y listas para cargar en **Taiga**. Formato por tarea: **nombre** — qué hace (en simple). *(Talle · Horas)*.
>
> **Roles:** `[BACKEND]` · `[FRONTEND]` · `[TEST]` · `[DOCUMENTACION]` · `[REVISION]`

---

## TH-01 · Gobernanza y Configuración Institucional

### EP-01 · Parámetros Globales

#### US-01 · Modificación y versionado de parámetros globales

1. **[G06] - [BACKEND] - Crear migración Flyway y entidad GlobalParameter con historial de versiones** — Script DDL, entidad JPA con key, value (jsonb), version (int). Seed de PAR-01..18. *(M · 6 h)*
2. **[G06] - [BACKEND] - Implementar caso de uso UpdateParameterCommand con versionado y vigencia no retroactiva** — Incrementa versión, valida rango, rechaza fechas retroactivas (RF-CFG-06), soporta Idempotency-Key (CA2). *(M · 6 h)*
3. **[G06] - [BACKEND] - Implementar endpoints REST GET/PUT de parámetros con autorización por rol** — Controladores GET y PUT con DTOs, @Valid, ADMIN escribe y PROFESOR solo lee (CA3). *(M · 6 h)*
4. **[G06] - [BACKEND] - Persistir registro en outbox_events dentro de la misma transacción del cambio** — Insertar OutboxMessage con payload GlobalConfigurationChanged en la misma tx (CA1). Requiere tabla de US-02. *(M · 6 h)*
5. **[G06] - [FRONTEND] - Diseñar pantalla de catálogo y formulario reactivo de edición de parámetros** — Componente Angular Standalone. Tabla de parámetros, modal de edición con validación de rango, motivo obligatorio. WCAG AA. *(M · 8 h)*
6. **[G06] - [FRONTEND] - Integrar servicio HTTP con BFF/Gateway y control visual de roles** — Servicio Angular que consume endpoints vía gateway. Ocultar edición para PROFESOR. Loading/error states. *(S · 4 h)*
7. **[G06] - [TEST] - Desarrollar tests unitarios de dominio: versionado, idempotencia y vigencia** — Incremento de versión (CA1), idempotencia por clave repetida (CA2), rechazo de fecha retroactiva y rango (CA4). *(M · 6 h)*
8. **[G06] - [TEST] - Desarrollar tests de integración con Testcontainers (PostgreSQL)** — Persistencia real de parámetro + historial + outbox. PROFESOR → 403 (CA3), ADMIN → 200 (CA1). *(M · 6 h)*
9. **[G06] - [DOCUMENTACION] - Congelar contrato OpenAPI 3 y diseñar diagrama de secuencia** — Spec springdoc de los 3 endpoints. Diagrama ADMIN → Gateway → Service → Outbox. Actualizar sdd/backend/docs/05. *(S · 4 h)*
10. **[G06] - [REVISION] - Peer review de PR, validación de DoD Nivel 1 y cierre en Taiga** — Clean Architecture, PMD/Checkstyle, sin secretos, build CI verde. *(S · 3 h)*

> **Orden:** DOCUMENTACION (9) y BACKEND (1+2) arrancan en paralelo. FRONTEND (5) arranca con el contrato congelado. TEST (7+8) arranca cuando existen las tareas 2+3. REVISION (10) siempre al final. Depende de US-02 (tabla outbox).

#### US-02 · Propagación del cambio de parámetro (Outbox + Kafka + Caché TTL)

> Historia 100% backend. No requiere frontend.

1. **[G06] - [BACKEND] - Crear tabla outbox_events con migración Flyway e implementar publisher programado** — DDL, entidad OutboxMessage, @Scheduled worker que lee pendientes, publica en topic administration.events y marca enviado. *(M · 8 h)*
2. **[G06] - [BACKEND] - Definir envelope estándar del evento GlobalConfigurationChanged** — Clase del envelope: eventId, eventType, occurredAt, correlationId, actorId, source, payload {key, value, version}. *(S · 4 h)*
3. **[G06] - [BACKEND] - Implementar reintentos con backoff exponencial y Dead Letter Topic** — Si Kafka no disponible, eventos quedan pendientes. Reintentos con backoff. Irrecuperables → DLT. Sin pérdida (CA2). *(M · 6 h)*
4. **[G06] - [BACKEND] - Implementar lógica de idempotencia por eventId y versión en consumidor de referencia** — Tabla ProcessedEvent(event_id, consumer). Descartar duplicados (CA4). Componente reutilizable, NO implementación en otros equipos. *(S · 4 h)*
5. **[G06] - [TEST] - Desarrollar test de integración del ciclo completo Outbox → Kafka → consumo** — Testcontainers (Kafka + PostgreSQL). Inserción en outbox → publicación → recepción. *(M · 8 h)*
6. **[G06] - [TEST] - Validar resiliencia ante caída del broker de Kafka** — Simular desconexión: eventos pendientes en outbox, reenvío al restaurar (CA2, BDD Esc. 2). *(M · 6 h)*
7. **[G06] - [TEST] - Validar descarte de eventos duplicados y de versión anterior** — Mismo eventId dos veces → segundo ignorado (CA4). *(S · 4 h)*
8. **[G06] - [DOCUMENTACION] - Documentar envelope estándar, catálogo de topics y contrato del consumidor** — Schema JSON, topic administration.events, guía para que Temas 03/05/08/10 implementen su consumidor con caché TTL 10 min. *(S · 4 h)*
9. **[G06] - [REVISION] - Peer review de concurrencia y transaccionalidad, verificación en Taiga** — Atomicidad de tx outbox, manejo de conexiones concurrentes, ausencia de race conditions. *(S · 3 h)*

> **Orden:** BACKEND (2) y DOCUMENTACION (8) arrancan en paralelo. BACKEND (1) necesita el envelope (2). BACKEND (3+4) necesitan (1). TEST (5+6+7) necesitan (1+3+4). REVISION (9) al final. No depende de ninguna otra US.

### EP-02 · Administración de la Plataforma

#### US-03 · Consola de gestión administrativa e integración de identidades (Tema 01)

> **Decisión de Arquitectura:** El Backoffice NO persiste cuentas de usuario, contraseñas ni roles en `administration_db`. La fuente única de verdad (SSOT) de identidades y salvaguarda cero-admin (`RF-ROL-05/06`) pertenece a **Tema 01**. La SPA de Angular opera como cliente de las APIs del Gateway de Tema 01 (`/api/admin/accounts/*`), y el backend de Tema 12 inspecciona los headers de confianza.

1. **[G06] - [BACKEND] - Implementar filtro de seguridad e inspección de headers propagados del Gateway (X-User-Roles, X-User-Id)** — Validación de tokens y extracción de contexto de autorización en `administration-service` sin persistencia local de usuarios. *(S · 4 h)*
2. **[G06] - [BACKEND] - Implementar cliente HTTP hacia Tema 01 vía Gateway para operaciones administrativas y auditoría delegada** — Cliente Feign/WebClient M2M para consultas de auditoría forense (`GET /api/audit`) y verificación de cuentas sin almacenar usuarios. *(M · 6 h)*
3. **[G06] - [FRONTEND] - Diseñar consola SPA para gestión de roles y cuentas consumiendo APIs de Tema 01 vía Gateway** — Componente Angular Standalone conectado a `/api/admin/accounts`. Tabla de administradores, modal de asignación y revocación con motivo obligatorio. *(M · 8 h)*
4. **[G06] - [FRONTEND] - Implementar salvaguardas visuales y control de sesión activa (bloqueo de auto-revocación)** — Bloqueo en interfaz para impedir que el ADMIN en sesión intente revocar sus propios privilegios (complemento visual de la regla en T01). *(S · 4 h)*
5. **[G06] - [TEST] - Desarrollar tests de integración del filtro de seguridad y autorización por headers del Gateway** — MockMvc: validar que requests con `X-User-Roles: ROLE_ADMIN` acceden a administración y `ROLE_TEACHER` recibe 403. *(M · 6 h)*
6. **[G06] - [TEST] - Desarrollar tests de integración y renderizado de componentes de la consola administrativa en Angular** — Jasmine/Karma o Vitest. Mock de endpoints de cuentas del Gateway, validación de estados reactivos y modales. *(M · 6 h)*
7. **[G06] - [DOCUMENTACION] - Especificar matriz de delegación de identidades con Tema 01 y contrato de borde (idea.pptx)** — Documentar que Tema 01 es el SSOT de identidades; mapear headers `X-Principal-Type`, `X-User-Id`, `X-User-Roles` y flujo SPA → Gateway → Tema 01. *(S · 4 h)*
8. **[G06] - [REVISION] - Auditoría de fronteras de microservicios, seguridad en PR y control en Taiga** — Garantizar que no se creó ninguna tabla de usuarios en `administration_db` y que la arquitectura permanece limpia. *(S · 3 h)*

> **Orden:** DOCUMENTACION (7) y BACKEND (1) arrancan en paralelo. FRONTEND (3+4) arranca con el contrato OpenAPI de Tema 01. BACKEND (2) y TEST (5) necesitan (1). TEST (6) necesita (3+4). REVISION (8) al final. Independiente de las bases de datos locales.

### EP-03 · Modelos LLM y Golden Set

#### US-04 · Registro de proveedores y modelos de IA

1. **[G06] - [BACKEND] - Crear migración Flyway y entidades ModelProvider y LlmModel con cifrado de API Keys** — Tablas con status ACTIVE/RETIRED, cifrado simétrico. Modelo nuevo → PENDING_REVIEW (CA1). *(M · 6 h)*
2. **[G06] - [BACKEND] - Implementar endpoint de registro de proveedores y catálogo con enmascaramiento** — POST (CA1 → 201). GET (CA2 — listado con estado). Claves enmascaradas sk-**** (CA3). *(M · 6 h)*
3. **[G06] - [BACKEND] - Implementar regla de estado inicial PENDING_REVIEW con bloqueo de activación** — Modelo recién registrado NO activable. Transición: PENDING_REVIEW → APPROVED → ACTIVE/STANDBY. *(S · 4 h)*
4. **[G06] - [FRONTEND] - Diseñar pantalla de gestión de proveedores y catálogo de modelos** — Formulario alta con campo API Key protegido (type=password). Tabla catálogo con badges de estado. Solo ADMIN. *(M · 8 h)*
5. **[G06] - [TEST] - Desarrollar tests de seguridad: cifrado en BD y enmascaramiento en respuestas** — API Key persiste cifrada, NINGÚN endpoint la devuelve en texto plano (CA3). *(M · 6 h)*
6. **[G06] - [TEST] - Validar bloqueo de activación para modelos en PENDING_REVIEW** — Intentar activar modelo recién registrado → rechazo. *(S · 4 h)*
7. **[G06] - [DOCUMENTACION] - Documentar endpoints de proveedores/modelos en OpenAPI y actualizar SDD** — Spec springdoc con esquemas. Documentar máquina de estados del modelo. *(S · 3 h)*
8. **[G06] - [REVISION] - Peer review de seguridad de credenciales y control en Taiga** — Que no se filtren API Keys en logs, respuestas ni repo. RULES-seguridad §3 y §5. *(S · 3 h)*

> **Orden:** DOCUMENTACION (7) y BACKEND (1) arrancan en paralelo. BACKEND (2+3) necesitan (1). FRONTEND (4) arranca con contrato. TEST (5+6) necesitan (2+3). REVISION (8) al final. Independiente de US-01/02/03.

#### US-05 · Sustitución y conmutación de modelos de IA

1. **[G06] - [BACKEND] - Implementar endpoint de activación con validación de estado APPROVED** — POST activate. Si estado ≠ APPROVED → 409 (CA2). Solo un modelo activo por función (RF-IA-25). *(M · 6 h)*
2. **[G06] - [BACKEND] - Implementar transición de estados ACTIVE ↔ STANDBY con Outbox** — Actual → STANDBY, nuevo → ACTIVE. Persistir ModelProviderChanged en outbox (misma tx). *(M · 6 h)*
3. **[G06] - [BACKEND] - Publicar evento ModelProviderChanged con modelo anterior y nuevo** — Payload: previousModelId, newModelId, function, activatedBy, activatedAt. Consumido por T04/T07 (CA3). *(S · 4 h)*
4. **[G06] - [FRONTEND] - Implementar modal de conmutación con advertencia de impacto** — Botón Activar en catálogo. Modal con modelo anterior/nuevo y confirmación explícita. *(S · 4 h)*
5. **[G06] - [TEST] - Desarrollar tests de transición de estados y rechazo de no aprobados** — Conmutación exitosa (BDD Esc. 1), rechazo 409 (BDD Esc. 2), evento publicado (BDD Esc. 3). *(M · 6 h)*
6. **[G06] - [DOCUMENTACION] - Documentar contrato del evento ModelProviderChanged y actualizar OpenAPI** — Schema del evento en docs/08. Endpoint en springdoc. *(S · 3 h)*
7. **[G06] - [REVISION] - Peer review y verificación en Taiga** — Unicidad de modelo activo y atomicidad de la transición. *(S · 2 h)*

> **Orden:** DOCUMENTACION (6) y BACKEND (1+2) arrancan en paralelo. BACKEND (3) necesita (2). FRONTEND (4) y TEST (5) arrancan cuando (1+2) están listos. REVISION (7) al final. Depende de US-04 (modelos registrados).

#### US-06 · Gestión del golden set y ejecución de calibración

1. **[G06] - [BACKEND] - Crear migración Flyway y entidades GoldenSet y CalibrationRun** — Tabla golden_set (version, entries jsonb). Tabla calibration_runs (model_id, error_avg, tolerance_ok, run_at). *(M · 6 h)*
2. **[G06] - [BACKEND] - Implementar endpoint para cargar y actualizar el golden set base** — POST/PUT golden-set. ADMIN sube o actualiza casos de referencia. Validar mínimo de casos. *(S · 4 h)*
3. **[G06] - [BACKEND] - Implementar servicio de calibración: envío a T07 y cálculo de error promedio** — POST golden-set/runs (CA1). Enviar casos a T07, recibir scores, calcular error sobre todos los casos (CA2). Sin casos → rechazo (BDD Esc. 2). *(L · 10 h)*
4. **[G06] - [BACKEND] - Implementar consulta de resultados históricos de calibración** — GET golden-set/runs (CA3). Listado con modelo, error, fecha, resultado. *(S · 4 h)*
5. **[G06] - [FRONTEND] - Diseñar tablero de resultados de calibración y gestión del golden set** — Arriba: golden set (ver/editar). Abajo: resultados de calibraciones (tabla + gráfico error temporal). *(M · 8 h)*
6. **[G06] - [TEST] - Desarrollar tests del algoritmo de cálculo de error con datos sintéticos** — Mocks de respuestas de T07. Cálculo correcto, caso sin golden set (BDD Esc. 2), persistencia del resultado. *(M · 6 h)*
7. **[G06] - [DOCUMENTACION] - Documentar estructura del golden set, flujo de calibración y contrato con T07** — Contrato de invocación a T07 y esquemas de datos del golden set en SDD. *(S · 4 h)*
8. **[G06] - [REVISION] - Peer review de algoritmos y control en Taiga** — Precisión numérica del cálculo y manejo de errores de T07. *(S · 3 h)*

> **Orden:** BACKEND (1) y DOCUMENTACION (7) arrancan en paralelo. BACKEND (2) y (3) necesitan (1) + contrato T07. FRONTEND (5) arranca con contrato. BACKEND (4) y TEST (6) necesitan (3). REVISION (8) al final. Depende de US-04 (modelos registrados).

#### US-07 · Aprobación por tolerancia (PAR-14) y fallback por deriva

1. **[G06] - [BACKEND] - Implementar regla de aprobación/rechazo basada en umbral PAR-14** — Error ≤ PAR-14 → APPROVED (CA1). Error > PAR-14 → REJECTED, bloquear activación (CA2). *(S · 4 h)*
2. **[G06] - [BACKEND] - Implementar monitor programado de deriva (drift detection)** — @Scheduled que re-ejecuta calibración del modelo activo solicitando corrida a T07. Si error supera PAR-14 → registrar deriva (CA3). *(M · 6 h)*
3. **[G06] - [BACKEND] - Implementar conmutación a modelo de respaldo y emisión de alerta** — Ante deriva: cambiar a STANDBY y emitir ModelDriftDetected (alerta crítica) hacia T11 y ADMIN. RF-IA-32. *(M · 6 h)*
4. **[G06] - [FRONTEND] - Diseñar indicador visual de deriva y banner de conmutación automática** — Badge de estado (APPROVED/REJECTED/DRIFTING). Banner de alerta ante conmutación. *(S · 4 h)*
5. **[G06] - [TEST] - Desarrollar tests de aprobación, rechazo y detección de deriva** — BDD Esc. 1 (error 3.4 → aprobado), BDD Esc. 2 (error 7.8 → rechazado), BDD Esc. 3 (error 6.2 → fallback + alerta). *(M · 6 h)*
6. **[G06] - [DOCUMENTACION] - Documentar algoritmo de evaluación, umbrales PAR-14 y reglas de drift** — Fórmula de cálculo y reglas de transición de estados en SDD. *(S · 3 h)*
7. **[G06] - [REVISION] - Peer review y verificación de DoD en Taiga** — Correctitud del monitor y atomicidad de la conmutación. *(S · 2 h)*

> **Orden:** BACKEND (1) y DOCUMENTACION (6) arrancan en paralelo. BACKEND (2) necesita (1). BACKEND (3) necesita (2). FRONTEND (4) arranca con contrato. TEST (5) necesita (1+2+3). REVISION (7) al final. Depende de US-06 (calibración funcional).

---

## TH-02 · Observabilidad y Soporte Académico

### EP-04 · Contratos de Lectura e Ingesta

#### US-08 · Ingesta de datos de los temas con deduplicación

> Historia 100% backend. No requiere frontend.

1. **[G06] - [BACKEND] - Crear migración Flyway y tabla de deduplicación ProcessedEvent** — DDL en reporting_db. Tabla processed_events(event_id UUID PK, consumer VARCHAR, processed_at TIMESTAMP). *(S · 4 h)*
2. **[G06] - [BACKEND] - Implementar consumidores de Kafka para los 6 temas proveedores** — Consumer groups para topics de T02, T04, T05, T07, T08, T10. Un adapter por tema que transforma payload en read model. *(L · 12 h)*
3. **[G06] - [BACKEND] - Implementar deduplicación por eventId en cada consumidor** — Verificar en processed_events. Si existe → descartar (CA2). Si no → procesar y registrar (CA1). *(S · 4 h)*
4. **[G06] - [BACKEND] - Configurar Dead Letter Topic para eventos malformados** — Mensajes con errores de formato van a DLT sin bloquear el partition consumer (CA3). *(S · 4 h)*
5. **[G06] - [TEST] - Desarrollar tests de integración: ingesta, deduplicación y DLT** — Testcontainers (Kafka + PostgreSQL). BDD Esc. 1 (nuevo → procesado), BDD Esc. 2 (duplicado → ignorado), BDD Esc. 3 (malformado → DLT). *(M · 8 h)*
6. **[G06] - [DOCUMENTACION] - Mapear contratos de lectura y esquemas JSON de los 6 temas** — Campos consumidos por tema. Consumer groups en docs/08. Entregable de coordinación con los otros equipos. *(M · 6 h)*
7. **[G06] - [REVISION] - Peer review de consumidores y control en Taiga** — Asignación de particiones, commit de offsets, tolerancia a fallos. *(S · 3 h)*

> **Orden:** DOCUMENTACION (6) es la PRIMERA tarea (acordar contratos con los 6 equipos). Sin contratos → sin consumidores. BACKEND (1) en paralelo con DOCUMENTACION. BACKEND (2) necesita (1) + contratos (6). BACKEND (3+4) necesitan (1). TEST (5) necesita (2+3+4). REVISION (7) al final. No depende de ninguna otra US; US-09/10/11/13 dependen de esta.

#### US-10 · Control de frescura de los datos y avisos

1. **[G06] - [BACKEND] - Implementar monitor programado de frescura por tema** — @Scheduled cada 2 min. Compara last_received_at con SLA de 15 min (CA1). *(M · 6 h)*
2. **[G06] - [BACKEND] - Implementar marcado de reportes y endpoint de estado de frescura** — Marcar reportes con isStale=true. GET health/freshness (CA2). Auto-retirar marca al normalizar (CA3). *(M · 6 h)*
3. **[G06] - [FRONTEND] - Diseñar badge de frescura en la cabecera de reportes** — "✓ Datos sincronizados" (verde) vs "⚠ Datos desactualizados: hace X min" (amarillo). Tooltip. *(S · 4 h)*
4. **[G06] - [TEST] - Desarrollar tests de detección de obsolescencia y normalización** — 16 min sin datos → isStale=true (BDD Esc. 1). Todos al día → sin aviso (BDD Esc. 2). Dato llega → marca retirada (BDD Esc. 3). *(M · 6 h)*
5. **[G06] - [DOCUMENTACION] - Especificar SLA de frescura en OpenAPI y actualizar SDD** — Endpoint springdoc. Regla de 15 min y normalización. *(S · 3 h)*
6. **[G06] - [REVISION] - Peer review y verificación en Taiga** — Monitor no consuma recursos excesivos. *(S · 2 h)*

> **Orden:** BACKEND (1+2), DOCUMENTACION (5) y FRONTEND (3) pueden arrancar todos en paralelo. TEST (4) necesita (1+2). REVISION (6) al final. Depende de US-08 (consumidores funcionales).

### EP-05 · Observabilidad, Reportes y Panel de Riesgo

#### US-09 · Exportación de reportes

> **Prioridad Could:** implementar SOLO si las Must y Should están completas.

1. **[G06] - [BACKEND] - Implementar endpoint asíncrono de solicitud de exportación** — POST /reporting/exports → HTTP 202 con exportId (CA1). Encolar generación en segundo plano. *(M · 6 h)*
2. **[G06] - [BACKEND] - Implementar generador de archivos en streaming para PDF y CSV** — Worker asíncrono por bloques sin cargar todo en memoria. CSV (tabulares) y PDF (formateado). *(L · 12 h)*
3. **[G06] - [BACKEND] - Implementar enlace temporal con vencimiento y validación de alcance por rol** — Token firmado con TTL. Si expiró → 410 (CA4). PROFESOR solo sus comisiones (CA3). GET exports/{exportId}. *(M · 6 h)*
4. **[G06] - [FRONTEND] - Diseñar diálogo de exportación y notificación de descarga** — Selector formato (CSV/PDF). Botón con spinner. Notificación cuando archivo listo con enlace. *(M · 6 h)*
5. **[G06] - [TEST] - Desarrollar tests de generación, seguridad de alcance y vencimiento** — BDD Esc. 1 (no bloqueante), BDD Esc. 2 (PROFESOR solo sus comisiones), BDD Esc. 3 (enlace vencido → 410). *(M · 6 h)*
6. **[G06] - [DOCUMENTACION] - Documentar flujo asíncrono de exportación en OpenAPI y SDD** — Endpoints springdoc. Diagrama solicitud → generación → notificación → descarga. *(S · 3 h)*
7. **[G06] - [REVISION] - Peer review de manejo de streams/memoria y control en Taiga** — Sin fugas de recursos en PDFs grandes. *(S · 3 h)*

> **Orden:** BACKEND (1) y DOCUMENTACION (6) arrancan en paralelo. BACKEND (2) necesita (1). FRONTEND (4) arranca con contrato. BACKEND (3) necesita (2). TEST (5) necesita (1+2+3). REVISION (7) al final. Depende de US-08.

#### US-11 · Read model y cálculo de riesgo por cohorte

> Historia 100% backend. No requiere frontend (resultado se expone en US-12).

1. **[G06] - [BACKEND] - Crear migración Flyway y esquema del Read Model analítico por cohorte** — Tabla cohort_summary(course_id, student_id, activity_days, approval_rate, last_activity_at, risk_level) en reporting_db. Índices para lecturas. *(M · 8 h)*
2. **[G06] - [BACKEND] - Implementar algoritmo de clasificación de nivel de riesgo** — ROJO = ≥14 días sin actividad O <40% aprobación. AMARILLO = ≥7 días sin actividad. VERDE = actividad reciente + ≥80% aprobación. *(M · 8 h)*
3. **[G06] - [BACKEND] - Implementar job programado de recálculo periódico** — @Scheduled que procesa foto analítica de todas las cohortes y actualiza risk_level (CA3). *(M · 6 h)*
4. **[G06] - [TEST] - Desarrollar tests exhaustivos de partición de equivalencia del algoritmo de riesgo** — BDD Esc. 1 (14 días + 40% → ROJO), BDD Esc. 2 (7 días → AMARILLO), BDD Esc. 3 (reciente + 80% → VERDE). Valores límite: 13 vs 14 días, 39% vs 40%. *(M · 6 h)*
5. **[G06] - [DOCUMENTACION] - Documentar reglas de cálculo de riesgo y esquema del Read Model** — Variables, umbrales y ponderaciones en SDD. *(S · 4 h)*
6. **[G06] - [REVISION] - Peer review de modelado analítico y verificación en Taiga** — Índices de BD, eficiencia del batch, correctitud del algoritmo. *(S · 3 h)*

> **Orden:** BACKEND (1) y DOCUMENTACION (5) arrancan en paralelo. BACKEND (2) necesita (1). TEST (4) puede arrancar con la interfaz del algoritmo. BACKEND (3) necesita (1+2). REVISION (6) al final. Depende de US-08. Se puede trabajar en paralelo con US-13.

#### US-12 · Panel del docente con RLS y alerta de riesgo

1. **[G06] - [BACKEND] - Implementar endpoint del panel docente con validación de matrícula vía T02** — GET /reports/courses/{courseId}/teacher. Pertenencia docente vía gateway → T02. No pertenece → 403 (CA3, BDD Esc. 2). *(M · 8 h)*
2. **[G06] - [BACKEND] - Aplicar Row Level Security por course_id y regla anti-comparación** — Setear app.current_course desde contexto validado. RLS con USING(course_id = ...). NO exponer métricas de otros docentes (CA4, BDD Esc. 3). *(M · 6 h)*
3. **[G06] - [BACKEND] - Emitir notificación interna ante transición a riesgo alto** — Alumno pasa a ROJO → evento StudentAtHighRisk (CA2, BDD Esc. 1). Consumido por T11. *(S · 4 h)*
4. **[G06] - [FRONTEND] - Diseñar vista de panel docente con semáforo de riesgo** — Tabla alumnos con semáforo (🔴🟡🟢). Filtros por nivel de riesgo. Tarjetas métricas de comisión. Accesible. *(M · 8 h)*
5. **[G06] - [TEST] - Desarrollar tests de RLS: docente A solo ve cohorte A** — PROFESOR A → cohorte A → 200 · cohorte B → 403 · intento ALL → 403 · ADMIN → global → 200. *(M · 6 h)*
6. **[G06] - [TEST] - Validar regla anti-comparación y emisión de alerta** — Respuesta NO contiene datos de otros docentes. Al pasar a ROJO se emite StudentAtHighRisk. *(S · 4 h)*
7. **[G06] - [DOCUMENTACION] - Especificar endpoints del panel, RLS y contrato de alertas** — OpenAPI springdoc. Política RLS y contrato con T11. *(S · 3 h)*
8. **[G06] - [REVISION] - Peer review de seguridad RLS y control en Taiga** — Auditoría estricta de aislamiento de datos. Punto de seguridad CRÍTICO. *(S · 3 h)*

> **Orden:** DOCUMENTACION (7) y BACKEND (1+2) arrancan en paralelo. BACKEND (3) necesita (1). FRONTEND (4) arranca con contrato. TEST (5+6) necesitan (1+2+3). REVISION (8) al final. Depende de US-11 (read model de riesgo). T02 debe exponer API de pertenencia docente.

#### US-13 · Indicadores consolidados con bloqueo de anonimato

1. **[G06] - [BACKEND] - Implementar servicio de agregación analítica de indicadores de plataforma** — CSAT (5 estrellas), engagement, tasas de aprobación/abandono desde read models de US-08. *(M · 8 h)*
2. **[G06] - [BACKEND] - Implementar mecanismo de protección de anonimato por umbral mínimo** — < N respuestas → "muestra insuficiente" (CA2, BDD Esc. 2). RF-ENC-04/12 y RULES-invariantes §11. *(S · 4 h)*
3. **[G06] - [BACKEND] - Implementar endpoint exclusivo ADMIN con regla anti-comparación** — GET /reports/platform. Solo ADMIN. Sin ranking ni ordenamiento de docentes. *(M · 6 h)*
4. **[G06] - [FRONTEND] - Diseñar dashboard de KPIs con tarjetas CSAT y advertencia de anonimato** — Tarjetas con estrellas, gráficos tendencia. Banner "muestra insuficiente" donde aplique. Solo ADMIN. *(M · 8 h)*
5. **[G06] - [TEST] - Desarrollar tests de anonimato estadístico y autorización ADMIN** — 3, 4, 5 respuestas → ocultamiento (BDD Esc. 2). No-ADMIN → 403. *(M · 6 h)*
6. **[G06] - [DOCUMENTACION] - Documentar políticas de privacidad y fórmulas de agregación** — Regla de anonimato, umbrales y fórmulas en SDD y OpenAPI. *(S · 3 h)*
7. **[G06] - [REVISION] - Peer review de privacidad y control en Taiga** — Cumplimiento de anonimato y ausencia de comparación docente. *(S · 2 h)*

> **Orden:** BACKEND (1+2) y DOCUMENTACION (6) arrancan en paralelo. BACKEND (3) necesita (1+2). FRONTEND (4) arranca con contrato. TEST (5) necesita (2+3). REVISION (7) al final. Depende de US-08. Se puede trabajar en paralelo con US-11.

#### US-14 · Umbrales de aviso y acceso al tablero

1. **[G06] - [BACKEND] - Implementar modelo de umbrales y endpoints CRUD exclusivo ADMIN** — Tabla alert_thresholds(indicator, min_value, max_value, enabled). Configurar umbrales (CA1). Solo ADMIN (CA3 → 403). *(S · 4 h)*
2. **[G06] - [BACKEND] - Implementar evaluador periódico de métricas contra umbrales y despacho de alertas** — @Scheduled que compara indicadores vs umbrales. Si indicador < umbral → ThresholdBreached (CA2, BDD Esc. 2). *(M · 6 h)*
3. **[G06] - [FRONTEND] - Diseñar panel de configuración de umbrales y lista de alertas activas** — Formulario de umbrales por indicador con validación de rangos. Lista de alertas con timestamp. *(S · 4 h)*
4. **[G06] - [TEST] - Desarrollar tests de evaluación de umbrales y autorización** — BDD Esc. 1 (configurar), BDD Esc. 2 (baja → alerta), BDD Esc. 3 (no-ADMIN → 403). Valores límite: en el umbral vs 1 punto abajo. *(M · 6 h)*
5. **[G06] - [DOCUMENTACION] - Documentar catálogo de umbrales y eventos de alerta en SDD** — Endpoints springdoc. Schema del evento ThresholdBreached. *(S · 3 h)*
6. **[G06] - [REVISION] - Peer review final y cierre en Taiga** — Verificación global de consistencia del backlog de reporting. *(S · 2 h)*

> **Orden:** BACKEND (1), DOCUMENTACION (5) y FRONTEND (3) arrancan en paralelo. BACKEND (2) necesita (1) + indicadores de US-13. TEST (4) necesita (1+2). REVISION (6) al final. Depende de US-13 (indicadores calculados).

---

## Totales y Cadenas de Ejecución

> **Totales:** 14 historias · **104 tareas** (distribuidas en 5 roles: BACKEND, FRONTEND, TEST, DOCUMENTACION, REVISION).
>
> **Cadenas de Trabajo Paralelo:**
> - **Frente A (Parámetros):** US-02 → US-01
> - **Frente B (Gobernanza / Identidad delegada):** US-03 (Independiente)
> - **Frente C (Pipeline de IA):** US-04 → US-05 → US-06 → US-07
> - **Frente D (Pipeline de Reporting):** US-08 → US-10 / US-11 → US-12 / US-13 → US-14 / US-09

---

## Correcciones Arquitectónicas y Justificación Técnica (El Porqué de los Cambios)

A continuación se detalla la fundamentación técnica de las correcciones aplicadas sobre el backlog para garantizar que la solución sea 100% inobjetable frente a la cátedra:

### 1. Eliminación de la Invasión de Bounded Context en US-03 (Identidad vs. Backoffice)
- **Problema detectado:** El borrador original de US-03 creaba una tabla `administrators` en `administration_db` e implementaba endpoints locales de altas, bajas lógicas y validación del último administrador.
- **Por qué es un error grave:** Viola el principio arquitectónico de *Single Source of Truth* (SSOT) y la delimitación canónica del sistema (`sdd/backend/docs/05-endpoints.md:5`: *"Auth y gestión de cuentas ADMIN pertenecen al Tema 01"*). Si Tema 12 creara su propia tabla de administradores, se provocaría una divergencia de estado (*split-brain*) donde un usuario dado de baja en Tema 01 seguiría activo en Tema 12, o viceversa. Además, la salvaguarda `RF-ROL-05` (cero-admin) no puede garantizarse transaccionalmente en una base secundaria.
- **Corrección aplicada:** Se refactorizó US-03 para que el backend de Tema 12 **NO cree ninguna tabla de usuarios**. La gestión de cuentas, asignación de roles y salvaguardas normativas se delegan íntegramente en las APIs del **Tema 01** a través del Gateway (`/api/admin/accounts/*`). La SPA de Backoffice actúa como cliente consumidor directo de esas APIs, y el backend de Tema 12 únicamente provee el filtro de inspección de los headers de confianza (`X-User-Roles`, `X-User-Id`).

### 2. Desmitificación de "Consumidor Puro" en EDA (Productor y Consumidor de Eventos)
- **Concepto aclarado:** El documento oficial del docente define al Backoffice como *"consumidor puro: sin dominio propio"*. Esto significa que no posee entidades operativas de negocio (no tiene desafíos, ni alumnos, ni saldos de monedas). 
- **Por qué no es "solo consumidor" en mensajería:** En Event-Driven Architecture, `administration-service` es el **Productor Exclusivo (Producer)** de los eventos normativos institucionales (`GlobalConfigurationChanged` y `ModelProviderChanged`). Si Tema 12 no emitiera estos eventos vía Transactional Outbox, los microservicios transaccionales (Temas 03, 05, 08 y 10) no podrían enterarse de los cambios en la economía ni en las reglas del juego.

### 3. Refutación del Antipatrón de "Emitir Reportes a Kafka"
- **Problema conceptual:** Asumir que el Backoffice "emite reportes" a través del bus de eventos.
- **Por qué es un antipatrón:** Un reporte es un documento denso, estructurado y formateado para lectura humana (dashboards, CSV, PDF). Publicar reportes completos en tópicos de Kafka genera saturación del broker (*payload bloat*), acoplamiento innecesario con la capa visual y no tiene utilidad para los microservicios operativos (el Runner o el Ledger jamás consumen un PDF).
- **Corrección aplicada:** Se implementó rigurosamente el patrón **CQRS**: `reporting-service` ingesta eventos atómicos livianos de 6 tópicos de entrada (US-08), materializa **Read Models** locales en `reporting_db` (US-11), y **SIRVE** los reportes bajo demanda vía HTTP REST hacia la SPA o mediante streaming asíncrono de CSV/PDF con enlaces temporales firmados (US-09).

### 4. Delimitación de Gobernanza (Tema 12) vs. Inferencia de Runtime (Tema 07)
- **Problema detectado:** Existía ambigüedad sobre si el Backoffice ejecutaba llamadas directas a las APIs de OpenAI o Anthropic.
- **Por qué es un error:** La regla no negociable del PRD y la arquitectura establece que **el Backoffice NO invoca LLMs**. La inferencia de código y la corrección en caliente pertenecen con exclusividad al **Tema 07**.
- **Corrección aplicada:** En US-06 (Tarea 3), se dejó explícito que para calibrar el Golden Set, el Backoffice despacha los casos de prueba hacia Tema 07 a través del Gateway (`T07EvaluationClient`); Tema 07 ejecuta las llamadas a los modelos externos y devuelve los puntajes para que Tema 12 calcule el error estadístico contra la tolerancia `PAR-14`.

### 5. Corrección de Alcance en US-02 (Consumidor de Referencia)
- **Problema detectado:** El backlog original incluía una tarea para *"implementar el consumidor con caché TTL en cada uno de los temas que consumen parámetros"*.
- **Por qué era inviable:** El equipo del Backoffice no tiene potestad ni acceso para escribir código dentro de los repositorios de los otros equipos (Temas 03, 05, 08, 10).
- **Corrección aplicada:** Se reemplazó por la provisión de un **componente de referencia reutilizable** y la documentación formal del contrato de eventos en OpenAPI/AsyncAPI, dejando la implementación de la caché en el ámbito de cada equipo consumidor.

### 6. Cumplimiento de Restricciones Éticas y Legales en Reporting
- **Salvaguarda de Privacidad (`PAR-18` / `RF-ENC-13`):** En US-13, se aplica el bloqueo estricto de métricas CSAT cuando una comisión registra menos de $N=5$ respuestas, impidiendo la desanonimización del estudiante.
- **Guarda Anti-Ranking Docente (`RF-RPT-07`):** En US-12 y US-13, se garantiza mediante **Row Level Security (RLS)** de PostgreSQL (`course_id = current_setting('app.current_course')`) que ningún docente pueda visualizar datos ni métricas de otros colegas, prohibiendo terminantemente rankings punitivos entre profesores.