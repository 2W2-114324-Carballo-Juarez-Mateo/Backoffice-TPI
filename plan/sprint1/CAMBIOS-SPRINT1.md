# Sprint 1 — Cambios en la planificación

Revisamos el plan contra el repo de cátedra, los contratos y las láminas de mensajería. Esto es todo lo que cambia.

**Informe completo con evidencia:** `plan/AUDITORIA-SPRINT1.md`
**Propuesta alternativa (back+front+test para todos):** `plan/sprint1/PROPUESTA-B-CAPAS.md`

---

## 1. Se entrega sobre el repo de cátedra, mono-módulo

`2026-P4-BE/tpi-backoffice` — una sola aplicación, Spring Boot 4, Java 21, paquete `ar.edu.utn.frc.tup.p4`.

Consecuencias:
- **No hay dos microservicios.** Hay **un datasource y dos esquemas**: `administration` y `reporting`.
- **No levantamos servidores de Eureka, Config Server ni Gateway propios** (la infraestructura de servidores centrales es de plataforma/T01). Nuestro microservicio **SÍ incluye el Discovery Client (`eureka-client`)** para registrarse y ser enrutado por el `tpi-api-gateway`.
- **Una sola tabla `processed_event`**, con columna `consumer`. Antes había dos.
- **Infra base inicial con dueño único (Luciano):** 6 h para consolidar la línea base del `pom.xml` en el PR #0 del Día 1 (evita merge conflicts concurrentes y colisiones de classpath en Git). Luego, cualquier desarrollador puede sumar librerías puntuales en su rama si su tarea lo requiere.

## 2. Ajustes en la capa de mensajería (Envelope y Headers)

El `EventoDTO` oficial de cátedra tiene **5 campos**: `eventId`, `eventType`, `timestamp`, `producer`, `payload`.
*(Cátedra ratificó formalmente que los topics y nombres de evento van en **INGLÉS**; los ejemplos de diapositivas en castellano eran ilustrativos).*

| Teníamos | Va |
|---|---|
| `EventEnvelope` propio | Consumir `com.utn.tpi.common.dto.EventoDTO` |
| `occurredAt` / `source` | `timestamp` / `producer` |
| `correlationId`, `actorId`, `role` en el envelope | **headers de Kafka** (metadatos de transporte) |
| Eventos en inglés (`GlobalConfigurationChanged`, `ParameterChanged`) | **Se mantienen en inglés** (confirmado por cátedra) |
| Topics en inglés (`challenge.events`, `course.events`, `administration.events`) | **Se mantienen en inglés** (confirmado por cátedra) |

➡️ **G1 se manda el Día 1** para formalizar con T11 los canales y la registración en la red.

## 3. El frontend entra al sprint — 34 h

Sin front no hay demo de un MVP conectado. Las 9 pantallas ya existen; **lo que no existe es la conexión**: cero `provideHttpClient`, cero llamadas HTTP, cero guards, y los 18 parámetros hardcodeados en un signal.

6 tareas nuevas (FE-1 a FE-6).

⚠️ **Hay dos repos Angular divergentes. Hay que elegir el canónico antes del Día 1.**

## 4. US-04 (Modelos de IA) sale al Sprint 2 — a decidir en el planning

1. Quien aprueba un modelo es **US-07**, que no está → entregábamos un catálogo donde nada se puede activar.
2. No es parte del circuito de la demo.
3. Tiene el conflicto Vault vs cifrado sin resolver → sin Definition of Ready.
4. Libera **31 h**, casi exactamente lo que cuesta el frontend.

## 5. US-08 entra acotada

Solo **T03** (`challenge.events`) y **T02** (`course.events`) — los únicos con contrato. T07 y T10 salen: sus contratos no están cerrados.

Había **tres versiones contradictorias** del reparto de consumidores. Queda una sola.

## 6. EP-04 · Contratos — 13 h, **sin asignar**

La épica existía **sin una sola tarea**. Las solicitudes a **T05 y T07 no existían**.

- **Pool de Julieta y Mateo**, se lo reparten sobre la marcha: **G1** mensajería a T11 (Día 1) · **G2** T05 · **G3** T07 · **G5** mapeo de topics.
- **Ana Paula documenta lo que ellos cierren:** **G4** registro formal en `CONTRATOS.md` (firma, fecha, responsable de cada lado, versión, evidencia) + **AP-6** carga en Taiga.

Hoy `CONTRATOS.md` registra *estado*, no *firma*: si un tema cambia su payload en el Sprint 2, no hay forma de demostrar qué se había acordado.

## 7. Capacidad recalculada — la planilla tenía la fórmula rota (`#REF!`)

`Teóricas = h/día × (días − ausencias)` → `Efectivas = teóricas − 8,5` → `Ajustada = efectivas × %`

| Integrante | Rol | Capacidad | Horas | % uso |
|---|---|---:|---:|---:|
| Paz, Luciano | MSII+PIV | 39,4 | 24 | 60,9 % |
| Carballo Juarez, Mateo | MSII+PIV | 41,5 | 22 + pool | ~67 % |
| Baigorria, Damian | PIV | 31,5 | 22 | 69,8 % |
| Cortez, Joaquin | PIV | 27,7 | 19 | 68,6 % |
| Disca, Julieta | MSII+PIV | 51,5 | 30 + pool | ~72 % |
| Maldonado, Valentina | MSII+PIV | 35,3 | 17 | **48,2 %** ⚠️ |
| Cerquatti, Máximo | MSII+PIV | 46,4 | 27 | 58,2 % |
| Cerasulo, Regina | MSII+PIV | 35,3 | 19 | **53,8 %** ⚠️ |
| Gianoli, Bruno | PIV | 28,4 | 20 | 70,4 % |
| Ducart, Ana Paula | MSII | 25,2 | 18 | 71,4 % |
| **TOTAL** | | **362,1** | **231** | **63,8 %** |

**Dos correcciones fuertes:**
- **Máximo: 46,4 h, no 32,4.** Trabaja los 10 días hábiles normales con 6 h/día y 90 % de dedicación (sin ausencias laborales). Pasa a ser el verificador del sprint.
- **Ana Paula: 25,2 h, no 45.** Es MSII pura, no codifica.
- **La capacidad del equipo es 362 h, no 431.** La diferencia son las 85 h de ceremonias y los % de dedicación.

⚠️ **Valentina (48 %) y Regina (54 %) quedaron bajas** al pasar las tareas de contrato al pool. Son huecos a llenar en el planning.

## 8. Nadie revisa su propio código. Los tests de integración, tampoco.

Antes, 3 de 5 historias tenían al autor como revisor. Ahora las 4 revisiones son independientes.

**Distinción importante:** los **unitarios los sigue escribiendo el autor** (TDD normal — quien escribe la entidad escribe sus propios unitarios). Lo que se movió a otra persona es solo la **integración con Testcontainers** (4 tareas), porque esas verifican los CA contra el contrato, no contra la implementación: si el mismo que interpretó mal un CA es quien escribe el test que lo verifica, el error se duplica en vez de cazarse.

El costo real: mete dependencia entre ramas — el test de integración espera a que el código de producción esté listo. Es una decisión con trade-off, no una obviedad; si el equipo prefiere que cada uno teste todo lo suyo, se revierte.

## 9. Cuatro tareas nuevas por criterios de aceptación que nadie cubría

| Tarea | Dueño | Por qué |
|---|---|---|
| `Idempotency-Key` en el endpoint (US-01 T12) | Joaquin | CA2 lo exige y no estaba en ninguna tarea |
| Evento de auditoría (US-01 T11) | Damian | Contrato **cerrado** con T01 que nadie cumplía |
| `@RestControllerAdvice` sobre `ErrorApi` (US-03 T9) | Regina | 14 códigos de error y ningún handler |
| Headers de Kafka de correlación (US-02 T11) | Bruno | Trazabilidad sin romper el DTO oficial |

Además: **la migración `outbox_message` va en un PR propio (#1, Día 2)**. Antes tenía que mergear el Día 1 desde una rama que cerraba el Día 3 — era imposible.

---

## ⚠️ Decidir antes del Día 1

1. **Mandar G1 a T11** — bloquea la primera tarea del sprint.
2. **¿Cuál de los dos repos Angular es el canónico?** — bloquea todo el front.
3. **¿US-04 sale del sprint?**
4. **US-03 no implementa ninguno de sus 4 CA** (asignar rol, último admin, auditoría). ¿Se renombra o se le agregan las tareas?
5. **API de Vault de T01** — bloquea el manejo de credenciales.
6. ~~¿Cobertura 80 % o 90 %?~~ **Decidido: 90 %**, el de nuestro DoD.
7. **¿Adoptamos la Propuesta B?** — ver abajo.

---

## Propuesta B — las tres capas para todos

Archivo aparte: `plan/sprint1/PROPUESTA-B-CAPAS.md`

**El problema que resuelve:** en esta división el reparto quedó por dominio, no por capa. **Back 8/9 · Front 4/9 · Test 3/9.** Máximo no toca una línea de producción; Luciano, Joaquin, Damian y Julieta no ven el front en todo el sprint.

**Qué hace:** parte las tareas más fino (23 de backend, 9 de front, 11 de test) para que **los 9 tengan al menos una de cada capa**, manteniendo que nadie testea ni revisa lo suyo.

| | Esta división | Propuesta B |
|---|---|---|
| Cobertura por capa | Back 8/9 · Front 4/9 · Test 3/9 | **9/9 · 9/9 · 9/9** |
| Trabajo | 231 h (63,8 %) | 246 h (67,9 %) |
| Dispersión | 23,2 pts | 13,7 pts |
| Context switch | bajo | alto |

Para un TPI donde se cursan MSII y PIV, que cada uno pueda mostrar las tres capas probablemente pesa más que el costo de context switch. **Se decide en el planning.**

---

**Archivos actualizados:** `plan/sprint1/tareas/README.md` · `tareas-sprint1.md` · `dev-1.md` a `dev-10.md`
**Archivos nuevos:** `plan/AUDITORIA-SPRINT1.md` · `plan/sprint1/CAMBIOS-SPRINT1.md` · `plan/sprint1/PROPUESTA-B-CAPAS.md`
