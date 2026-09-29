# Revisión de PR — Sprint 2

> **Para qué sirve:** que cada revisor/a sepa **qué mirar** en la PR que le toca (matriz §9 de `tareas-sprint2.md`) y que el autor sepa **qué le van a pedir** antes de abrirla.
> **Regla de la retro:** todo comentario de revisión queda **en GitHub** (en la PR o en la línea). Lo que se hable por WhatsApp se vuelca a la PR antes de aprobar.

---

## 1 · Cómo se revisa

1. **Nadie revisa ni testea lo suyo** (matriz §9).
2. La PR se abre **con la rama terminada**. Si está a medias, no se pide review.
3. El revisor **ejecuta** el verify (o confirma la salida pegada y su fecha): en `develop` no hay CI de tests.
4. Cada comentario lleva una etiqueta:

| Etiqueta | Significa | ¿Bloquea el merge? |
|---|---|---|
| `bloqueante:` | Rompe un CA, una regla del repo o la seguridad | Sí |
| `sugerencia:` | Mejora con motivo | No (el autor decide y responde) |
| `pregunta:` | Falta entender algo | Sí, hasta que se responda |
| `nit:` | Detalle de estilo | No |

5. **Aprobar** = "lo leí, lo ejecuté y cumple la checklist". Si no se ejecutó, se comenta "aprobado sin ejecutar" y no cuenta como aprobación.

### Plantilla de descripción de PR (la completa el autor)

```markdown
## Qué hace
<!-- 2 o 3 líneas -->

## Tareas y CA
- Taiga: #___ (tarea) · #___ (historia)
- CA cubiertos: CA1 ✅ · CA2 ✅ · CA3 ⏳ (motivo)

## Cómo probarlo
<!-- endpoint + rol, o pantalla + rol -->

## Verificación
<!-- salida final de `mvn -B clean verify` o `npm run verify` y fecha/hora de la corrida -->

## Revisores (matriz §9)
Testea: @___ · Revisa: @___

## Checklist del autor
- [ ] Rama sincronizada con `develop` antes de pedir review
- [ ] Sin archivos de otros dueños (si toqué uno, está explicado acá)
- [ ] Flyway: uso solo la versión que tengo reservada
- [ ] OpenAPI / docs actualizados
```

---

## 2 · Checklist general — Backend

- [ ] Código, comentarios, `@DisplayName` y mensajes de excepción en **inglés**; commit en español con Conventional Commits.
- [ ] Sin `var`, sin `@Autowired` en producción (en tests, preferir constructor con `@TestConstructor`), sin nombres de clase totalmente calificados.
- [ ] Inyección por constructor: `private final` + `@RequiredArgsConstructor`.
- [ ] Entidades con `@Getter`/`@Setter`/`@NoArgsConstructor`, **nunca `@Data`**; `@ManyToOne`/`@OneToOne` en `LAZY`.
- [ ] `ModelMapper` inyectado (bean único de `MappersConfig`), nunca `new ModelMapper()`; mapper con test campo por campo.
- [ ] Controllers delgados; toda regla en el servicio; errores con `ErrorApi` vía `GlobalExceptionHandler`.
- [ ] **Cada endpoint con `@PreAuthorize` usando `SecurityExpressions`** (no literales).
- [ ] Rutas bajo `${app.api.private-path}`.
- [ ] Migración con **su** número reservado; si la tabla de `reporting` tiene `course_id`, trae su política RLS.
- [ ] Tests con `metodo_escenario_resultado`, `// GIVEN / WHEN / THEN`, límites y caminos de error; `@MockitoBean` (Boot 4), nunca `@MockBean`.
- [ ] Sin secretos ni keys en código, propiedades o logs.
- [ ] `mvn -B clean verify` en verde (Checkstyle, PMD, JaCoCo ≥ 0,90).

## 3 · Checklist general — Frontend (repo `2026-PIV-TPI-FE`)

- [ ] Solo archivos bajo `src/app/features/admin/` del Tema 12 (y la línea propia de `admin.routes.ts`).
- [ ] **No toca** `angular.json`, `package*.json`, `tsconfig*`, configs de lint/format ni `.github/**` (el CI del FE rechaza la PR).
- [ ] Standalone, `inject()`, `input()`/`output()` (no decoradores `@Input`/`@Output`), control flow nativo, sin `console.*` (usar el logger del repo).
- [ ] Componentes de `@2026-p4-fe/ui` (`Generic*`, entrada única).
- [ ] Llamadas a `/api/backoffice/...` (nada nuevo contra `/api/administration` o `/api/reports`).
- [ ] Accesibilidad: teclado, foco visible, color **y** texto, contraste AA.
- [ ] Textos de UI en español; código y commits en inglés.
- [ ] `npm run verify` en verde (sin `ng build` local).

---

## 4 · Checklist por PR del Sprint 2

### 4.1 · Arrastre y contratos

**S2-00 · Contratos compartidos (Luciano) — revisan Máximo + Mateo**
- [ ] **No hay lógica**: solo interfaces, DTOs, enums, OpenAPI esqueleto y rutas stub.
- [ ] `ReportDimension` **no** tiene `TEACHER`; `RiskLevel` tiene exactamente `RED`, `YELLOW`, `GREEN`.
- [ ] `DataFreshnessDto` tiene `asOf`, `stale`, `thresholdMinutes`, `sources[]`.
- [ ] Cada interfaz tiene dueño de implementación anotado en su Javadoc (evita que dos la implementen).
- [ ] No incluye migraciones.
- [ ] FE: cada ruta stub usa `loadPlaceholder` y está en una línea propia.

**06B-T1 · Orden del outbox por clave (Luciano) — revisa Valentina, testea Máximo**
- [ ] `NOT EXISTS` de una fila `PENDING` más vieja **con la misma clave de partición** (el outbox genérico la guarda en la columna `param_key`: `DomainEventOutboxImpl` hace `setParamKey(event.getPartitionKey())`).
- [ ] Una fila vieja en backoff **frena** a las nuevas de su clave; una fila en `DEAD_LETTER` **no** las frena para siempre.
- [ ] Hay índice que soporte la subconsulta (estado + clave + `created_at`), o se justifica por qué no hace falta.
- [ ] Filas de **otras** claves siguen saliendo en paralelo.

**V21 · Topics v3 del registro de contratos (Joaquín) — revisa Valentina**
- [ ] V14 **no** se edita; V21 hace `UPDATE ... WHERE topic = '<viejo>'` (idempotente).
- [ ] `ReadContractControllerTest` y `SourceContractRepositoryTest` usan los nombres nuevos.
- [ ] La pantalla 14 muestra `courses.events`, `challenges.events` y `accounting.events`.

**07-T1 · PAR-14 y P-12 · PAR-12 (Damián) — revisa Mateo**
- [ ] PAR-14: conserva las claves sembradas en V2 (`promedio`, `dimension`); rechaza faltantes, no numéricos, negativos, `promedio > dimension` y `> 100`; mensajes en inglés y accionables.
- [ ] P-12 (si el grupo lo confirmó): V24 siembra el valor **y** su fila v1 en el historial (mismo patrón que V8); regla `1 ≤ initialLives ≤ maxLives`; `AGENTS.md` corregido **en la misma PR**.

**01-IT · Test de integración de US-01 (Mateo) — revisa Regina**
- [ ] Solo el test (sin el `jsonKafkaTemplate` de la rama vieja).
- [ ] Testcontainers PostgreSQL real; cubre versión, bloqueo optimista, historial e `Idempotency-Key`.
- [ ] Usa la API actual del registro (PAR-01..24), no la de septiembre.

**Contratos C1, C2, C3, C5, C6, C8, C9 (docs en el repo) — revisa el dueño de la historia que los consume**
- [ ] Envelope de **6 campos** (`eventId, eventType, eventVersion, timestamp, producer, payload`) y topics v3.
- [ ] Lo pedido coincide con §3.1 del plan (C3: distribución 1–5, abstenciones, dimensión, `courseClosed`, pertenencia).
- [ ] Se actualizan **juntos** el `.md` del tema, la tabla de estado y **la fila de firma del responsable** en `CONTRATOS.md` (regla del propio archivo).

**Wiki de G06 (Ana) — revisa el dueño de cada historia** (no es una PR: se comenta en la página de Taiga)
- [ ] Nombre con el formato de la cátedra: "G06 - TEMA".
- [ ] Están todas las secciones de `template-proyecto-por-grupo`: descripción, historias enlazadas, diagramas en orden (DER → BPMN → Clases → Estados → Secuencias → Microservicios) con enlace editable y explicación, observaciones y endpoints.
- [ ] Los ejemplos de request/response son **reales** (los que pasó el dueño), con rutas `/api/backoffice/...` y sin datos personales.
- [ ] El DER coincide con las migraciones actuales (V1–V17 y las nuevas del S2).
- [ ] No duplica contenido de otra página: enlaza.

### 4.2 · LLM (HU04/HU05)

**04-T1 · Cliente HTTP a T07 (Máximo) — revisa Bruno, testea Luciano**
- [ ] URL base y timeouts en propiedades tipadas; timeout de 3 s.
- [ ] Reintento **solo en GET**.
- [ ] `problem+json` de T07 → `LlmProviderException`/`LlmModelException` con `ErrorApi`; un 401/403 de T07 **no** sale como 500.
- [ ] Propaga `X-Request-Id` y `traceparent`.
- [ ] El stub queda activo con el flag apagado (arranque local sin T07).

**04-T2 + 04-T3 · Proveedores reales (Regina) — revisa Bruno**
- [ ] La key **nunca** aparece en logs, respuestas, `toString()` ni excepciones (buscar el valor en la salida del test).
- [ ] El DTO de alta tiene la key como solo escritura; la lista muestra `sk-****`.
- [ ] Pantalla 09: sin datos en memoria; estados de carga, vacío y error.

**05-T1 + #276 + 05-T3 · Modelos y conmutación (Mateo) — revisa Joaquín**
- [ ] 409 de T07 (modelo no aprobado) llega al usuario con mensaje claro; la unicidad del activo la decide T07.
- [ ] Pantalla 10: se eliminó `MOCK_MODELS`.
- [ ] El modal muestra actual → nuevo y exige confirmación explícita; foco atrapado y `Esc` cancela.

### 4.3 · Reporting (HU11, HU12, HU10-bis)

**#303 · Read model V18 (Damián) — revisa Máximo**
- [ ] Esquema `reporting`; único `(course_id, student_id)`; índices por `course_id` y `(course_id, risk_level)`.
- [ ] Sin FK hacia tablas de otros slices (solo IDs).
- [ ] `CohortSummaryQuery` de solo lectura.

**#311 · Acceso y RLS (Máximo) — revisa Mateo (crítico), testea Luciano**
- [ ] `ENABLE` **y `FORCE ROW LEVEL SECURITY`** (la app es dueña de las tablas).
- [ ] La política usa `current_setting('app.current_course', true)`: **sin valor → cero filas**.
- [ ] El valor se fija con `set_config('app.current_course', :courseId, true)` **con parámetro bind**, nunca concatenando un `SET LOCAL` (inyección).
- [ ] `ALL` solo para ADMIN.
- [ ] Flag de T02 apagado → PROFESOR **denegado** (fail-closed), ADMIN funciona.
- [ ] Curso ajeno → **403** (no lista vacía, no 404).
- [ ] Todo corre dentro de `@Transactional` (si no, `SET LOCAL` no aplica).

**#305 · Proyector (Valentina) — revisa Máximo, testea Regina**
- [ ] Idempotente: procesar dos veces el mismo `ingested_event` deja el mismo resultado.
- [ ] Usa el `timestamp` del envelope (cuándo pasó), no `received_at`.
- [ ] Nunca hace `UPDATE`/`DELETE` sobre `ingested_event` (tiene trigger append-only).
- [ ] `eventType` desconocido → se saltea con log, no rompe el lote.
- [ ] Período configurable y siempre menor que PAR-23.
- [ ] T05/T10 detrás de flag.

**#304 + #312 · Riesgo y evento (Damián) — revisa Máximo / Mateo, testean Regina y Luciano**
- [ ] Clasificador puro (sin Spring), umbrales de `reporting.risk.*`.
- [ ] Límites: 10/11 días, 4/5, 39/40 %, 60/61 %, 69/70 %; R-1 y R-2 según lo que decidió el grupo.
- [ ] `STUDENT_AT_HIGH_RISK` **solo** cuando `previous != RED` y `new == RED`, en la **misma transacción** que el update.
- [ ] Recalcular dos veces no emite dos eventos.
- [ ] Payload solo con IDs (sin nombres ni emails).

**#310 · Panel docente (Regina) — revisa Mateo, testea Luciano**
- [ ] `@PreAuthorize` ADMIN o PROFESSOR **+** `ReportScopeResolver`.
- [ ] No devuelve promedios de otros cursos ni datos de otros docentes (CA4).
- [ ] Incluye `DataFreshnessDto`.
- [ ] Una consulta por pedido (sin N+1); < 2 s con datos de demo.

**10-M1 · Frescura en reportes (Valentina) — testea Regina**
- [ ] Calculada al leer con `IngestionStatsQuery`; **sin** `@Scheduled` nuevo.
- [ ] PAR-23 leído del registro con 15 por defecto.
- [ ] `asOf` = el evento más viejo entre las fuentes requeridas; `stale` si cualquiera supera PAR-23; fuente sin eventos → `stale` y marcada.

**#313 · Panel FE (Damián) — revisa Mateo, specs Joaquín**
- [ ] Semáforo con color **y** texto; navegable por teclado.
- [ ] 403 → mensaje claro, sin datos.
- [ ] Badge de frescura reutilizado de #291 (no copiado).

### 4.4 · US-15, HU13 y stretch

**15-T1 + 15-T3 · Catálogo y plantillas (Joaquín) — revisa Valentina, testea Máximo**
- [ ] Métricas y dimensiones solo por enum; cada métrica declara su fuente y su disponibilidad.
- [ ] El `config` de una plantilla se valida contra la lista blanca **al guardar**.
- [ ] Solo el dueño ve/edita sus plantillas; una plantilla ajena responde 404 (no revela que existe).
- [ ] V20 con política RLS si tiene `course_id`.

**15-T2 + 15-T4 · Motor y `run` (Bruno) — revisa Valentina, testea Máximo**
- [ ] **Ningún valor del usuario se concatena a SQL**: métrica/dimensión → fragmento fijo por enum; filtros → parámetros bind.
- [ ] Métrica o dimensión desconocida → 400 con `ErrorApi`.
- [ ] Sin `TEACHER`; PROFESOR con un curso ajeno en el filtro → 403.
- [ ] Métricas de encuesta: solo agregadas, con PAR-18 **y** curso cerrado.
- [ ] Período máximo y tamaño de página máximo; `DataFreshnessDto` en la respuesta.
- [ ] Ejecuta dentro de `ReportScopeResolver` + RLS.

**HU13 · KPIs CSAT (Mateo) — revisa Damián, testea Joaquín**
- [ ] V22 **sin autor y sin timestamp preciso** (a lo sumo el día o el período); con RLS.
- [ ] KPI = % 4–5 y % 1–2 sobre respuestas emitidas; abstenciones fuera del denominador pero informadas.
- [ ] PROFESOR: puntajes solo con `respuestas ≥ PAR-18` **y** curso cerrado; si no, solo el conteo.
- [ ] `/platform` solo ADMIN; desglose por curso **ordenado por curso, sin ranking**.

**#322 · Dashboard KPIs (Valentina) — revisa Damián**
- [ ] Estados "muestra insuficiente" y "disponible al cierre del curso".
- [ ] Gráficos con alternativa textual.

**15-T8 · Builder (Luciano) — revisa Regina, specs Damián**
- [ ] Solo ofrece métricas de `GET /reports/metrics`; las no disponibles se ven deshabilitadas con el motivo.
- [ ] No existe opción de agrupar por docente.
- [ ] Guardar plantilla con validación y feedback accesible.

**HU14 · Alertas (Regina, stretch) — revisa Joaquín, testea Máximo**
- [ ] CRUD solo ADMIN; `min ≤ max`.
- [ ] El evaluador no duplica una alerta activa para el mismo umbral; resuelve la alerta cuando vuelve al rango.
- [ ] **No publica `THRESHOLD_BREACHED`** en Kafka.

**HU09 · Export (Damián, stretch) — revisa Luciano**
- [ ] El job guarda el **alcance de quien lo pidió** y lo vuelve a aplicar al ejecutarse (en segundo plano no hay `SecurityContext`).
- [ ] Solo quien lo pidió puede descargarlo.
- [ ] CSV protegido contra **inyección de fórmulas** (celdas que empiezan con `=`, `+`, `-`, `@`).
- [ ] `EXPORT_READY` lleva el `exportId`, no los datos.
- [ ] Hereda anti-comparación y anonimato.

### 4.5 · Frontend de arrastre

**#3512 · Guards (Máximo) — specs Mateo, revisa Joaquín**
- [ ] Todas las rutas solo-ADMIN tienen guard; GESTOR y PROFESSOR no entran por URL directa.
- [ ] No rompe las rutas de T01 en `admin.routes.ts`.

**#291 · Badge (Valentina) — revisa Joaquín**
- [ ] Incluye los fixes de la review de la PR #91.
- [ ] El componente queda reutilizable (recibe la frescura por `input()`).

**05-N1 · `/api/backoffice` (Luciano) — revisa Joaquín**
- [ ] No queda ninguna llamada a `/api/administration` o `/api/reports`.
- [ ] Se retiró el parche de `proxy.conf.backoffice-gateway.cjs`.
- [ ] Los alias del backend **siguen** (no se tocan en esta PR).

**#3331 · Solo lectura PROFESSOR (Bruno) · #1657 · 2FA (Regina) · 05-N2 (Valentina) — revisa Joaquín**
- [ ] PROFESSOR ve parámetros sin botones de edición y sin acceso a la ruta de edición.
- [ ] 2FA: solo lectura del estado que expone T01.
- [ ] El dashboard oculta lo que el rol no puede usar.

---

## 5 · Comentarios listos para las ramas pendientes

> Para pegar en la PR (si existe) o en un issue. Van sin culpa: son decisiones de alcance, no errores personales.

**`feature/mvp-s6-golden-set-runs` (Bruno)**
> Esta rama implementa MAE, veredicto y corridas **locales**. Con la Opción A (acordada después) eso lo hace T07 y el Backoffice queda como fachada, por eso la PR #30 se cerró. Propuesta: tag `archive/s6-golden-set-local` y borrar la rama, para que nadie la mergee por error. `ToleranceEvaluator` ya lee PAR-14 con las claves reales (`promedio`, `dimension`) y sirve de referencia para 07-T1 (validación de PAR-14), que sí es nuestra.

**`feature/us-01-testcontainers` (Mateo)**
> El test de integración de US-01 aporta y nunca tuvo PR. Propuesta: rama nueva desde `develop` con **solo** `GlobalParameterServiceIntegrationTest`, adaptado al registro actual. El `jsonKafkaTemplate` de `KafkaProducerConfig` ya no hace falta (la auditoría va por outbox) y agrega un bean de producción sin uso.

**`feature/us-02-envelope` (Mateo)**
> `develop` ya arma el envelope de 6 campos en `DomainEventOutboxImpl` y tiene el record `EventEnvelope` en `reporting/dtos/events`. Mergear esta rama dejaría dos `EventEnvelope` y dos formas de armarlo. Propuesta: cerrar.

**`feature/contratos-alineados-drive` (Mateo)**
> Superada por el mapeo de topics ratificado con T11 (v3, PR #47). Propuesta: cerrar.

**FE `fix/admin-export-service-spec` (Mateo)**
> `develop` ya tiene tu fix en una versión más estricta (`3899e9d` + `495e29b`, con el cast de tipos). El merge solo produciría un conflicto en `export.service.spec.ts`. Propuesta: cerrar y borrar.

**FE `feature/tema-12-admin-route-guards` (Máximo)**
> Rama lista, sin conflictos con `develop`. Falta abrir la PR (#3512) y avisar a los dueños de las partes 03, 04, 06, 10 y 14.

**FE `feature/mvp-s7-ingestion-ui` (Valentina)**
> El badge re-aplicado con los fixes de la review (`d175ca1`) no llegó a `develop`. No hay que rehacerlo: sincronizar con `develop` y abrir la PR (#291).

**`feature/contexto-sprint1` y `fix/development-doc-real-workflow` (Luciano)**
> Subir a `develop` solo lo que sirve de trazabilidad (plan MVP del S1, auditoría Skill Hub, `DEVELOPMENT.md`). `Contexto.md` es de sesión y la regla de inglés de `AGENTS.md` ya entró por la #28.
