# Propuesta B — Sprint 1 con las tres capas para todos

> **Alternativa a la división vigente** (`tareas/tareas-sprint1.md`). Misma capacidad, mismo alcance, mismo repo.
> **La diferencia:** las tareas están partidas más fino para que **cada integrante tenga al menos una tarea de BACKEND, una de FRONTEND y una de TEST**.
> **Ana Paula queda afuera de esta regla**: es MSII pura, su bloque es análisis y documentación.

---

## 1 · El problema que resuelve

En la división vigente el reparto quedó por **dominio**, no por **capa**:

| | BACK | FRONT | TEST |
|---|:--:|:--:|:--:|
| Luciano | 6 | **0** | **0** |
| Mateo | 3 | 1 | **0** |
| Damian | 3 | **0** | 1 |
| Joaquin | 3 | **0** | **0** |
| Julieta | 2 | **0** | 3 |
| Valentina | 2 | 1 | **0** |
| Máximo | **0** | **0** | 4 |
| Regina | 2 | 1 | **0** |
| Bruno | 1 | 2 | **0** |

**Back 8/9 · Front 4/9 · Test 3/9.** Máximo terminaba siendo "el que testea" sin tocar una línea de producción; Luciano, Joaquin, Damian y Julieta no veían el front en todo el sprint.

Para un TPI eso es un problema: **MSII+PIV cursan las dos materias** y la nota sale de lo que cada uno pueda mostrar.

## 2 · Por qué hizo falta partir las tareas

Con el tamaño de tareas anterior **no entraba**. Las tareas de backend de US-01 eran de 6 h, y Damian (31,5 h), Joaquin (27,7 h) y Bruno (28,4 h) no llegaban a sumar back + front + test sin pasarse del 85 %.

Los cambios de granularidad:

| Antes | Ahora | Por qué |
|---|---|---|
| US-01 T1 · entidad + migración + seed (6 h) | **B-10** entidad + migración (4 h) · **B-11** seed + validación de rango (4 h) | El seed depende de AP-7; la entidad no. Se pueden paralelizar. |
| US-01 T3 + T12 (10 h) | **B-13** endpoints (5 h) · **B-15** `Idempotency-Key` (4 h) | La idempotencia es una preocupación separada del CRUD. |
| FE en 6 tareas (34 h) | **FE en 9 tareas (41 h)** | Se conectan dos pantallas más: administración y estado de ingesta. No es relleno: suman a la demo. |
| TEST en 8 tareas (46 h) | **TEST en 11 tareas (53 h)** | Se agregan tests de contrato del evento y de manejo de errores — dos huecos reales. |

**Costo total: +15 h** sobre la división vigente. Sigue holgado: **246 h sobre 362,1 = 67,9 %**.

## 3 · Las reglas que se mantienen

1. **Nadie testea su propio código.** Las 11 tareas de TEST las escribe alguien que no escribió esa parte.
2. **Nadie revisa su propio código.** Las 4 REVISION son independientes.
3. **Ana Paula no codifica.** MSII: análisis, diagramas y registro de contratos.
4. **Las tareas de contrato (G-1 a G-3, G-5) siguen sin asignar** — pool de Julieta y Mateo.
5. **Línea base de Infra centralizada en Luciano (PR #0):** Consolida dependencias iniciales (incluyendo Discovery Client) para evitar merge conflicts el Día 1; luego cualquier integrante puede sumar librerías en su rama si su tarea lo requiere.

---

## 4 · Reparto

| Dev | Integrante | Cap. | BACK | FRONT | TEST | Otros | **Total** | **% uso** |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | Paz, Luciano | 39,4 | 20 | 3 | 6 | — | **29** | 73,6 % |
| 2 | Carballo Juarez, Mateo | 41,5 | 12 | 5 | 5 | pool 6 | **28** | 67,5 % |
| 3 | Baigorria, Damian | 31,5 | 9 | 5 | 5 | rev 3 | **22** | 69,8 % |
| 4 | Cortez, Joaquin | 27,7 | 10 | 5 | 4 | — | **19** | 68,6 % |
| 5 | Disca, Julieta | 51,5 | 7 | 4 | 13 | pool 7 | **31** | 60,2 % |
| 6 | Maldonado, Valentina | 35,3 | 10 | 5 | 6 | doc 3 | **24** | 68,0 % |
| 7 | Cerquatti, Máximo | 46,4 | 11 | 4 | 8 | doc 4 · rev 3 | **30** | 64,7 % |
| 8 | Cerasulo, Regina | 35,3 | 9 | 5 | 3 | doc 4 · rev 3 | **24** | 68,0 % |
| 9 | Gianoli, Bruno | 28,4 | 7 | 5 | 3 | doc 3 · rev 3 | **21** | 73,9 % |
| 10 | Ducart, Ana Paula | 25,2 | — | — | — | 18 | **18** | 71,4 % |
| | **TOTAL** | **362,1** | **95** | **41** | **53** | **57** | **246** | **67,9 %** |

**Los 9 tienen las tres capas.** Dispersión: 13,7 puntos (60,2 %–73,9 %) — más ancha que la división vigente (9,5), que es el precio de garantizar la cobertura por capa.

---

## 5 · Tareas de BACKEND — 95 h

| ID | Tarea | Dev | h |
|---|---|---|---:|
| B-01 | Infra: adopción del repo (`.tpi`, `app.dev-*`, README) + dependencias base del pom (incluye Discovery Client) | Luciano | 3 |
| B-02 | Infra: `docker-compose.yml` (PostgreSQL + Kafka + app) | Luciano | 2 |
| B-03 | Infra: `verify.yml` a `develop` + umbral de JaCoCo | Luciano | 1 |
| B-04 | Migración `outbox_message` + perfiles y datasource PostgreSQL | Luciano | 5 |
| B-05 | Publisher Outbox → Kafka + **clave de partición** | Luciano | 5 |
| B-06 | Adoptar el `EventoDTO` oficial + mapeo de `ParameterChanged` / `GlobalConfigurationChanged` | Mateo | 6 |
| B-07 | Reintentos con backoff exponencial + Dead Letter Topic | Mateo | 6 |
| B-08 | Idempotencia por `eventId` + versión (consumidor de referencia) | Bruno | 4 |
| B-09 | Headers de Kafka de correlación (`correlationId`, `actorId`, `role`) | Bruno | 3 |
| B-10 | Entidad `GlobalParameter` + historial + migración | Damian | 4 |
| B-11 | Seed PAR-01..18 (sin externos) + validación de rango | Máximo | 4 |
| B-12 | `UpdateParameterCommand`: versionado y no retroactividad | Damian | 5 |
| B-13 | Endpoints GET/PUT de parámetros con autorización por rol | Joaquin | 5 |
| B-14 | Outbox en la misma transacción del cambio | Joaquin | 5 |
| B-15 | `Idempotency-Key`: tabla, TTL y captura en el endpoint | Regina | 4 |
| B-16 | Emisión del evento de auditoría (contrato cerrado con T01) | Máximo | 4 |
| B-17 | Filtro de seguridad e inspección de headers del Gateway | Luciano | 4 |
| B-18 | Cliente HTTP hacia T01 (auditoría delegada) | Regina | 5 |
| B-19 | `@RestControllerAdvice` sobre el `ErrorApi` de cátedra | Máximo | 3 |
| B-20 | Migración `processed_event` (esquema `reporting`) | Julieta | 3 |
| B-21 | Consumidores `challenge.events` + `course.events` | Valentina | 6 |
| B-22 | Deduplicación por `eventId` | Valentina | 4 |
| B-23 | Dead Letter Topic de ingesta | Julieta | 4 |

## 6 · Tareas de FRONTEND — 41 h

| ID | Tarea | Dev | h |
|---|---|---|---:|
| FE-1 | **Capa HTTP:** `provideHttpClient` + `environment` + interceptor de errores que mapea `ErrorApi` | Mateo | 5 |
| FE-2 | Interceptor de headers: `Idempotency-Key` + `X-Request-Id` | Luciano | 3 |
| FE-3 | Servicio de parámetros (GET/PUT) + modelos | Joaquin | 5 |
| FE-4 | Pantalla de parámetros conectada (reemplazar el signal hardcodeado) | Bruno | 5 |
| FE-5 | Guards por rol + estado de sesión (ADMIN escribe, PROFESOR lee) | Regina | 5 |
| FE-6 | Feedback de operación: loading, error, «aplica de ahora en adelante» | Julieta | 4 |
| FE-7 | Pantalla de administración conectada (auditoría delegada de T01) | Damian | 5 |
| FE-8 | Pantalla de estado de ingesta (eventos procesados, DLT, frescura) | Valentina | 5 |
| FE-9 | Navegación por rol + limpieza del catálogo mock (sacar PAR-03/06/07, corregir «24 parámetros») | Máximo | 4 |

> **Criterio de asignación:** cada uno toma la pieza del front que corresponde al backend que escribió o que mejor conoce. Joaquin hace el servicio de parámetros porque escribe el endpoint; Regina los guards porque escribe el borde de identidad; Valentina la pantalla de ingesta porque escribe los consumidores. Así el front no es trabajo ciego.

## 7 · Tareas de TEST — 53 h

| ID | Tarea | Dev | h | Testea código de |
|---|---|---|---:|---|
| TS-1 | Unitarios de dominio: versionado, rango, retroactividad | Luciano | 6 | Damian |
| TS-2 | Integración US-01 con Testcontainers (parámetro + historial + outbox) | Valentina | 6 | Damian, Joaquin |
| TS-3 | Integración del ciclo Outbox → Kafka → consumo | Julieta | 8 | Luciano, Mateo |
| TS-4 | Resiliencia ante caída del broker | Julieta | 5 | Luciano, Mateo |
| TS-5 | Descarte de duplicados y de versión anterior | Joaquin | 4 | Bruno |
| TS-6 | Filtro de seguridad y autorización por headers (MockMvc) | Damian | 5 | Luciano |
| TS-7 | Ingesta, deduplicación y DLT | Mateo | 5 | Valentina, Julieta |
| TS-8 | Unit tests del front: servicio de parámetros + interceptores (Vitest) | Bruno | 3 | Mateo, Joaquin |
| TS-9 | **Smoke E2E del flujo de la demo** | Máximo | 4 | todos |
| TS-10 | **NUEVO** · Tests de contrato del evento: que el JSON publicado cumpla el `EventoDTO` oficial (campos, naming, formato de `timestamp`) | Máximo | 4 | Mateo |
| TS-11 | **NUEVO** · Tests de manejo de errores: que cada código de negocio devuelva el `ErrorApi` correcto | Regina | 3 | Máximo |

> **TS-10 y TS-11 son huecos reales que la división anterior no cubría.** Sin TS-10, que el envelope cumpla el contrato de cátedra queda librado a la revisión de código. Sin TS-11, los CA de 400/403/409/422 no se verifican en ningún lado.

## 8 · Documentación y revisión — 26 h

| ID | Tarea | Dev | h |
|---|---|---|---:|
| D-1 | Envelope, catálogo de topics y contrato del consumidor | Máximo | 4 |
| D-2 | OpenAPI de parámetros | Bruno | 3 |
| D-3 | Matriz de delegación de identidades con T01 | Regina | 4 |
| D-4 | Mapeo de contratos de lectura y esquemas (T03 y T02) | Valentina | 3 |
| R-1 | Peer review US-01 | Bruno | 3 |
| R-2 | Peer review US-02 (concurrencia y transaccionalidad) | Regina | 3 |
| R-3 | Peer review US-03 (fronteras de microservicios) | Damian | 3 |
| R-4 | Peer review US-08 (particiones, offsets, tolerancia a fallos) | Máximo | 3 |

## 9 · EP-04 · Contratos — 13 h · **sin asignar**

Pool de **Julieta y Mateo**. Se lo reparten sobre la marcha.

| ID | Tarea | h |
|---|---|---:|
| G-1 | **Solicitud de contrato de mensajería a T11** — topics, envelope, tipo de `timestamp`, headers, artefacto compartido, naming del payload | 4 |
| G-2 | Solicitud de contrato a T05 (entregas, topic, PAR-19/20) | 3 |
| G-3 | Solicitud de contrato a T07 (proveedor de modelo, deriva, PAR-22) | 3 |
| G-5 | Tabla de mapeo topics oficiales ↔ Backoffice + plan de migración | 3 |

**G-1 sale el Día 1, sí o sí.** Lo que se cierre lo registra Ana Paula.

## 10 · Ana Paula — 18 h

| ID | Tarea | h | Día |
|---|---|---:|---|
| AP-7 | **Catálogo de tipos, rangos y categorías de PAR-01..18** — desbloquea B-11 y B-12 | 4 | **1–2** |
| AP-1 | BPMN · cambio de parámetro global end-to-end | 5 | 1–3 |
| AP-2 | Diagrama de Microservicios — ubicar al Tema 12 en el mapa de red oficial | 4 | 1–3 |
| G-4 | **Registro formal de contratos** en `CONTRATOS.md` (firma, fecha, responsable, versión, evidencia) | 3 | 7–10 |
| AP-6 | Carga y sincronización en Taiga (diagramas + contratos) | 2 | 9–10 |

---

## 11 · Verificación de las reglas

**Cobertura por capa — los 9 tienen las tres:**

| Dev | BACK | FRONT | TEST |
|---|---|---|---|
| Luciano | B-01…05, B-17 | FE-2 | TS-1 |
| Mateo | B-06, B-07 | FE-1 | TS-7 |
| Damian | B-10, B-12 | FE-7 | TS-6 |
| Joaquin | B-13, B-14 | FE-3 | TS-5 |
| Julieta | B-20, B-23 | FE-6 | TS-3, TS-4 |
| Valentina | B-21, B-22 | FE-8 | TS-2 |
| Máximo | B-11, B-16, B-19 | FE-9 | TS-9, TS-10 |
| Regina | B-15, B-18 | FE-5 | TS-11 |
| Bruno | B-08, B-09 | FE-4 | TS-8 |

**Independencia — nadie testea ni revisa lo suyo:** verificado tarea por tarea en las tablas de §7 y §8.

---

## 12 · Qué perdés al elegir esta propuesta

Para que lo decidan con los dos lados sobre la mesa:

| Costo | Detalle |
|---|---|
| **Más context switch** | Cada uno pasa por 3 capas en vez de concentrarse en una. En un sprint de 10 días eso se siente. |
| **Dispersión más ancha** | 13,7 puntos contra 9,5 de la división vigente. Luciano (73,6 %) y Bruno (73,9 %) quedan arriba; Julieta (60,2 %) abajo. |
| **+15 h de trabajo** | Dos pantallas más y dos tests más. Entran, pero es alcance adicional. |
| **Los tests pesados se concentran** | TS-3 (8 h) y TS-4 (5 h) quedan en Julieta porque nadie más los absorbe sin pasarse. Máximo, que es quien más capacidad tiene, queda con tests más chicos para dejarle lugar al backend. |

**Qué ganás:** los 9 tocan back, front y test — que para un TPI donde se cursan las dos materias es probablemente lo que más importa.

---

> Si esta propuesta se aprueba, reemplaza a `tareas/tareas-sprint1.md` y hay que regenerar los `dev-N.md` con estos IDs.
> Capacidad y fórmula: `tareas/README.md`. Informe completo: `../AUDITORIA-SPRINT1.md`.
