# Prompt para NotebookLM — Propuesta de Requerimientos del Backoffice

> **Cómo usarlo:** pegá este documento como fuente/instrucción en NotebookLM. Al final hay 3 pedidos explícitos (texto, audio, video). Ajustá el idioma de salida si querés (el contenido es en español).

---

## 1. Rol y contexto

Actuá como **explicador académico** de un proyecto universitario de backend. Tu tarea es ayudar a un equipo de estudiantes a **entender y defender** la propuesta de requerimientos funcionales (RF) y no funcionales (RNF) de su módulo, frente al profesor.

**Proyecto:** Plataforma de Aprendizaje Gamificado de Programación y Desarrollo de Software (trabajo integrador, 4to cuatrimestre). La plataforma se divide en **12 temas** (microservicios) según el documento oficial del docente. **Nuestro equipo es el Tema 12 — Backoffice**, y es un **"consumidor puro: sin dominio propio"**: administra la plataforma y reporta, pero NO es dueño de identidad, cursos, desafíos ni economía; los **consume** o los **lee** vía contratos.

**Stack técnico:** Java 21 · Spring Boot 3 · Maven · PostgreSQL · Kafka · Eureka · Spring Cloud Gateway (de plataforma, Tema 01) · JPA/Hibernate · Clean Architecture.

**Nuestros 2 microservicios propietarios:**
1. **Administration & Configuration Service** → configuración global (parámetros PAR-01..23) + gestión del proveedor LLM (exclusiva de ADMIN).
2. **Reporting & Analytics Service** → reportes docentes, panel del profesor, métricas, exportación y alertas.

**Consumimos:** identidad, auth, roles, 2FA, auditoría y retención del **Tema 01**; la cohorte del **Tema 02**; y leemos datos de los Temas 02/04/05/07/08/10 para reportes/métricas.

---

## 2. Requerimientos Funcionales (RF) — Backoffice

### Configuración global (Administration)
- **RF-CFG-01**: el ADMIN administra configuraciones globales.
- **RF-CFG-04**: los parámetros de economía (PAR) son globales y solo los administra el ADMIN.
- **RF-CFG-05**: separación de ámbitos; el PROFESOR no puede modificar parámetros globales.
- **RF-CFG-06**: los cambios de parámetros aplican solo hacia adelante (nunca se recalculan XP/monedas históricos).
- Parámetros **PAR-01 a PAR-23 (PAR-24 asignado al Tema 01)** (base PRD PAR-01..18; el registro es genérico y extensible).

### Gestión del proveedor LLM (exclusiva de ADMIN)
- **RF-IA-ADM-01**: alta/sustitución/baja de proveedores y modelos de LLM, exclusiva de ADMIN y auditada (RF-IA-35).
- **RF-IA-ADM-02**: asignación modelo ↔ función (tutor, evaluador, moderador, generador, RAG) — global (RF-IA-23/24).
- **RF-IA-ADM-03**: evaluador de uso de IA con un único modelo activo (RF-IA-25).
- **RF-IA-ADM-04**: cambio de modelo evaluador sujeto a calibración (RF-IA-28).
- **RF-IA-ADM-05**: golden set base a nivel plataforma + calibración dentro de tolerancia (RF-IA-30/31).
- **RF-IA-ADM-06**: detección de deriva con re-calibración y alerta (RF-IA-32).
- **RF-IA-ADM-07**: trazabilidad de cohortes evaluadas con más de un modelo (RF-IA-33).
- El **Tema 07 (Evaluación LLM)** consume esta configuración; nosotros solo la administramos.

### Reportes docentes, métricas, exportación y alertas (Reporting & Analytics)
- **RF-RPT-01**: reportes docentes — el PROFESOR consulta sus cohortes; el ADMIN el consolidado de plataforma.
- **RF-RPT-02**: panel de métricas de curso — satisfacción (KPIs CSAT 5 estrellas, encuestas agregadas y anónimas), engagement, aprobación/abandono.
- **RF-RPT-03**: panel del profesor con indicador de alumno en riesgo (para más adelante).
- **RF-RPT-04**: exportación de datos (resúmenes y reportes en CSV/PDF).
- **RF-RPT-05**: alertas configurables (para más adelante).
- **RF-RPT-06**: frescura máxima de 15 minutos en los datos de lectura.
- **RF-RPT-07**: sin comparación entre docentes.
- **RF-RPT-08**: no exposición de datos fuera de ámbito.
- **RF-RPT-09**: encuestas solo como agregados anónimos (RF-ENC-04/12).
- **RF-RPT-10**: contratos de lectura con los seis temas (02/04/05/07/08/10) — dependencia crítica del sprint 1.

### Consumido del Tema 01 (no se implementa)
Identidad, auth, 2FA, roles/permisos, sesión, auditoría, retención y API Gateway son del Tema 01. El Backoffice los **consume** para operar y autorizar sus endpoints. Principio: *validar ≠ autorizar* — el gateway valida el token; la autorización la decide el servicio dueño de la regla.

### Fuera del alcance (otros temas)
Cursos y Matrícula (T02) · Motor de Desafíos (T03) · Teóricos y Encuestas (T04) · Desafíos Prácticos (T05) · Sandbox (T06) · Evaluación LLM (T07) · Banco (T08) · Mercado (T09) · Roadmap y Progreso (T10) · Social y Notificaciones (T11). El Backoffice los **lee** (02/04/05/07/08/10) para reportes y métricas, sin implementarlos.

---

## 3. Requerimientos No Funcionales (RNF)

- **Seguridad**: toda operación administrativa autenticada; autorización por rol y recurso.
- **Autenticación**: usuario + contraseña + **2FA obligatorio** (lo provee T01).
- **Autorización en dos niveles**: en el gateway (valida token) y en el microservicio propietario (revalida identidad, rol, permisos y alcance). El gateway no es frontera suficiente.
- **Aislamiento de datos**: Database per Service (cada microservicio es dueño de su base; sin FKs entre bases; relaciones por IDs).
- **Regla no negociable**: toda llamada síncrona entre microservicios pasa por el **gateway** (no hay comunicación directa); lo asincrónico viaja por **Kafka**.
- **Escalabilidad**: soportar 120 sesiones concurrentes (objetivo del PRD).
- **Resiliencia**: timeouts, retry con backoff, circuit breaker; operaciones asíncronas reintentables sin duplicar efectos (idempotencia por `event_id`).
- **Consistencia/idempotencia**: operaciones críticas idempotentes; consumidores toleran duplicados.
- **Trazabilidad**: correlation ID propagado en todo el recorrido.
- **Auditoría**: separada de los logs técnicos (la persiste T01; el Backoffice emite los eventos).
- **Borrado lógico**: no existe hard delete en producción académica.
- **Retención**: 5 años configurable, sin purga automática, decisión de ADMIN (consumida de T01).
- **Privacidad**: encuestas anónimas por diseño (sin vínculo autor ↔ respuesta).
- **Observabilidad**: logs estructurados, health checks, métricas.
- **Configuración**: secretos fuera del repositorio (variables de entorno/secret manager).
- **API**: REST documentada con OpenAPI/Swagger; versionado de API.
- **Fiabilidad de eventos**: **Outbox + at-least-once + idempotencia**.
- **Rate limiting y 429**: límites en el gateway; respuesta 429 con `Retry-After`; **Idempotency-Key** en operaciones administrativas críticas; front con **single-flight** (una sola petición en vuelo).
- **Frescura**: datos de lectura del Backoffice con frescura máxima de 15 minutos.
- **Sin comparación entre docentes** en reportes.

---

## 4. Arquitectura en resumen (para el contexto)

- **2 servicios propietarios** (Administration & Configuration, Reporting & Analytics) + consumo de T01/T02 y lecturas de 02/04/05/07/08/10.
- **API Gateway de plataforma (T01)** como única puerta; **Eureka** (discovery), **Config Server**, **Kafka** (eventos), **PostgreSQL** por servicio.
- **Patrones**: Clean Architecture por capas, CQRS (command/query), Command, Specification, Adapter (clientes por tema), Null Object (fallback), Observer + Outbox, Unit of Work, Idempotency Key.
- **Reglas clave**: consumidor puro · contratos de lectura en sprint 1 · proveedor LLM exclusivo de ADMIN · cambios de config hacia adelante · encuestas solo agregados anónimos.

---

## 5. Trabajo con el resto de los microservicios (integración entre equipos)

> Esta sección es para que el equipo pueda **explicarle a sus compañeros** cómo se relaciona el Backoffice con los otros temas. Incluí esta info en los 3 recursos.

### 5.1 Los 12 temas y quién es dueño de qué

| Tema | Dueño | Nuestro rol |
|---|---|---|
| **T01** Identidad y Usuarios | otro equipo | **Consumimos** (auth, roles, 2FA, auditoría, retención) + el **API Gateway de plataforma** |
| **T02** Cursos y Matrícula | otro equipo | **Consumimos** la cohorte (`course_id`) y la matrícula (pertenencia docente) |
| **T03** Motor de Desafíos | otro equipo | **Leemos** |
| **T04** Teóricos y Encuestas | otro equipo | **Leemos** (solo agregados anónimos) |
| **T05** Desafíos Prácticos | otro equipo | **Leemos** |
| **T06** Sandbox / Runtime | otro equipo | — |
| **T07** Evaluación LLM | otro equipo | **Leemos** + **consume nuestra config de proveedores** |
| **T08** Banco | otro equipo | **Leemos** |
| **T09** Mercado | otro equipo | — |
| **T10** Roadmap y Progreso | otro equipo | **Leemos** |
| **T11** Social y Notificaciones | otro equipo | — |
| **T12 Backoffice** | **nosotros** | Administración (PAR + proveedor LLM) + reportes/métricas/export/alertas |

### 5.2 Cómo nos integramos (contratos cross-team)

- **Síncrono:** toda llamada entre microservicios **pasa por el gateway** (no hay comunicación directa). Ej: para autorizar un reporte docente, consultamos la matrícula al **T02** a través del gateway.
- **Asíncrono:** por **Kafka** con **Outbox + idempotencia**. Ejemplos:
  - El ADMIN cambia un **PAR** → publicamos `GlobalConfigurationChanged` → los **Temas 03/05/08/10** lo leen y lo aplican.
  - El ADMIN cambia el **proveedor LLM** → publicamos `ModelProviderChanged` → el **T07** lo consume.
  - El Backoffice **lee** eventos de los Temas 02/04/05/07/08/10 para construir reportes y métricas.
- **Regla "cada entidad tiene un único dueño":** nadie toca la base de datos del vecino; todo se coordina por eventos o APIs por el gateway.
- **Qué le damos a los compañeros:** la configuración de PAR (que ellos aplican en vez de hardcodear) y la gestión de proveedores (que T07 usa). Sin nuestra config, esos temas no tienen de dónde leer los valores.
- **Qué necesitamos de ellos:** los **contratos de lectura** (dependencia crítica del sprint 1), la matrícula (T02) y la autorización (T01).
- **Frases para conversar con otros equipos:** "¿publicás el evento X en el topic Y con este payload?", "¿exponés la pertenencia a cohorte por el gateway?", "¿leés `GlobalConfigurationChanged` o tenés los PAR hardcodeados?", "acordamos los contratos de lectura en la sesión de integración".

### 5.3 Analogías para entender lo técnico (obligatorio usarlas)

> El equipo **no conoce todas las tecnologías**. En los 3 recursos explicá cada concepto técnico con una **analogía simple** + la versión técnica con su nombre.

- **API Gateway** = el portero/guardia del edificio: todo entra por la misma puerta, valida la credencial y reparte a la oficina correcta.
- **Microservicio** = un departamento con su tarea: cada uno hace lo suyo y se coordina por mensajes, sin meterse en el trabajo del otro.
- **Kafka** = el archivo/registro central de avisos: cada consumidor lee desde donde quedó (offset) y puede re-leer lo publicado (replay); lo recogen cuando quieren, sin llamarse entre sí.
- **Outbox** = el cartero anota el aviso en su libreta antes de salir: si el correo se cae, el aviso no se pierde.
- **Idempotencia** = si te llega el mismo aviso dos veces, pagás/respondés una sola vez.
- **Database per Service** = cada equipo tiene su propio cuaderno; nadie escribe en el cuaderno del vecino (se avisan por notas/eventos).
- **Clean Architecture** = la lógica importante está en el centro (las reglas) y lo externo (base de datos, HTTP) son enchufes intercambiables.
- **Consumidor puro** = el Backoffice es como un **tablero de control**: no fabrica los datos, solo administra la configuración y muestra/lee lo que producen los demás.
- **Validar ≠ autorizar** = el portero comprueba que tu credencial sea válida (validar), pero decidir si podés entrar a esa oficina (autorizar) lo decide la oficina dueña.
- **Frescura ≤ 15 min** = los números del tablero pueden tener hasta 15 minutos de atraso; no tienen que ser en tiempo real.

---

## 6. Pedido de salida (UN solo recurso: video)

Generá **un video explicativo de 6 a 8 minutos** que explique la propuesta completa (RF + RNF + arquitectura + integración con los otros equipos) de forma visual y con analogías, para que el equipo la entienda y la defienda.

**Estructura del video (por escena: qué se VE + qué se DICE):**
1. Intro (0:00-0:30): título "Backoffice — Tema 12", proyecto, equipo.
2. Qué es el Backoffice y por qué "consumidor puro" (tablero de control): 2 servicios propietarios + consumo (T01/T02) + lectura (02/04/05/07/08/10).
3. Los 2 microservicios y qué hacen (Administration & Configuration — PAR-01..23 + proveedor LLM; Reporting & Analytics — reportes, panel, métricas CSAT, exportación, alertas).
4. Requerimientos funcionales clave (RF-CFG-06 hacia adelante · RF-IA-ADM proveedor exclusivo · RF-RPT reportes/métricas).
5. Requerimientos no funcionales y arquitectura (autorización 2 niveles, Database per Service, sync por gateway, Kafka + Outbox + idempotencia, rate limiting/429, secretos).
6. Integración con los otros 11 temas (qué damos: PAR→03/05/08/10, proveedores→T07; qué recibimos: contratos de lectura, matrícula T02, auth T01; contratos = dependencia crítica).
7. Planificación: backlog general con 2 temas → 5 épicas → **14 historias** (SP Fibonacci + horas por tarea; Must = 43 SP).
8. Cierre y defensa (argumentos + posibles preguntas del profesor con respuestas).

**Reglas:**
- Narración en español, tono docente, con **analogías** para cada concepto técnico (Gateway = portero, Kafka = buzón, Outbox = cartero con libreta, consumidor puro = tablero de control, Database per Service = cuaderno propio, validar ≠ autorizar).
- Para cada escena, indicar **qué mostrar en pantalla** (diapositivas/diagramas/bullets).
- Mantener fidelidad al contenido de las secciones 1-5; no inventar requisitos; resaltar que el Backoffice NO implementa identidad, cursos, desafíos ni economía.