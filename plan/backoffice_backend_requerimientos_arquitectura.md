# Especificación de Requerimientos y Arquitectura Backend — BackOffice

> **Proyecto:** Plataforma de Aprendizaje Gamificado de Programación y Desarrollo de Software  
> **Alcance del equipo:** Backend del módulo **BackOffice**  
> **Documento:** Requerimientos funcionales, no funcionales y arquitectura de microservicios  
> **Versión:** 1.1  
> **Estado:** Propuesta para revisión con docentes  
> **Stack decidido:** Java + Spring (Spring Boot 3, Maven)  
> **Stack en detalle:** ver Sección 6.2

---

## 1. Propósito del documento

Este documento define el **alcance, requerimientos y arquitectura backend del BackOffice** que desarrollará el equipo.

El PRD general define **qué debe hacer la plataforma**, pero deliberadamente no define el cómo: no establece arquitectura, modelo de datos, diseño técnico ni división de servicios. Esa definición corresponde a los equipos de desarrollo.

Por lo tanto, este documento no pretende diseñar toda la plataforma. Se concentra en las capacidades administrativas que necesita el BackOffice y en los servicios backend que las soportan.

### 1.1 Alcance del equipo

Alcance según el documento oficial del docente (**Tema 12 — Backoffice** de `TUP_PIV_BE_PROPUESTA_ARQ.pdf`). El Backoffice es **"consumidor puro: sin dominio propio"**: administra la plataforma y reporta, pero no es dueño de identidad, cursos, desafíos ni economía.

**Servicios propietarios (2):**

1. **Administration & Configuration** — *pedido para empezar*:
   - Registro de parámetros **PAR-01..PAR-24** (base PRD PAR-01..18; nota: el doc del profe menciona hasta PAR-24; el registro es genérico/extensible).
   - **Gestión del proveedor LLM, exclusiva de ADMIN** (RF-IA-23/24/25/28/31/32/35).
2. **Reporting & Analytics**:
   - **Reportes docentes** y **exportación de datos**.
   - **Panel del profesor con indicador de alumno en riesgo** (para más adelante).
   - **KPIs con CSAT de 5 estrellas**, **alertas configurables** (para más adelante).
   - **Frescura ≤ 15 minutos** en los datos de lectura; **sin comparación entre docentes**.

**Consumo (no implementar):** autenticación, roles/permisos, 2FA, auditoría y retención → **Tema 01 (Identidad y Usuarios)**; cohorte/curso → **Tema 02 (Cursos y Matrícula)**; y **contratos de lectura con los seis temas** que proveen datos (02, 04, 05, 07, 08, 10).

**Reglas no negociables (de la plataforma):** gateway como única puerta · registro dinámico · **toda llamada síncrona entre servicios pasa por el gateway** · base exclusiva por servicio · asincrónico por el bus de eventos · cada entidad con un único dueño.

### 1.2 Fuera del alcance del equipo

El BackOffice **no implementa** ningún otro tema (los consume o los lee):

- **Tema 01 Identidad y Usuarios**: registro, autenticación, 2FA, roles/permisos, token/sesión, auditoría, retención, API Gateway. → **Consume** (auth, autorización y auditoría de sus operaciones vienen de acá).
- **Tema 02 Cursos y Matrícula**: alta de curso, inscripción, padrón, invitación, ciclo de vida de la cohorte. → **Consume** (la cohorte es la clave `course_id`).
- **Tema 03 Motor de Desafíos / Tema 04 Teóricos y Encuestas / Tema 05 Desafíos Prácticos / Tema 06 Sandbox / Tema 07 Evaluación LLM / Tema 08 Banco / Tema 09 Mercado / Tema 10 Roadmap y Progreso / Tema 11 Social y Notificaciones**: dominios de otros equipos. → Solo **lectura** para reportes/métricas (02/04/05/07/08/10).
- **Frontend del BackOffice (definido, Caso A)**: app **Angular SSR** + **BFF por experiencia** (del equipo BackOffice) + **Nginx** (web + reverse proxy `/api/*` → BFF) con build Docker en 2 etapas y `envsubst`. El front consume por el **API Gateway de plataforma (T01)** vía el BFF; es de la materia **Front** y no suma microservicios de dominio. Detalle: `frontend/arquitectura-despliegue`.
- Interfaz de alumno, IDE, instancia evaluada, marketplace de plugins, integración con sistemas universitarios, funcionalidades fuera del MVP.

El BackOffice puede **consultar información** de estos dominios cuando el PRD otorgue esa capacidad a ADMIN/PROFESOR, mediante **contratos de lectura** (eventos/APIs a través del gateway), pero no implementa el módulo.

---

# 2. Base funcional y criterios de alcance

El PRD establece tres roles principales:

| Rol | Alcance |
|---|---|
| ADMIN | Acceso total a la plataforma y su información |
| PROFESOR | Crea y administra cursos, desafíos y configuración a nivel curso |
| ALUMNO | Aprende, practica, compite e interactúa |

Para este equipo, los roles relevantes son principalmente **ADMIN** y **PROFESOR**.

El PRD también establece que:

- Todos los ADMIN tienen los mismos permisos.
- Un ADMIN no puede auto-eliminarse.
- Los ADMIN solo pueden ser creados/eliminados por otros ADMIN.
- El sistema nunca puede quedar sin un ADMIN activo.
- La baja de un ADMIN requiere confirmación reforzada.
- Las configuraciones globales pertenecen al ADMIN.
- Los parámetros económicos son globales y solo los administra ADMIN.
- Los cambios administrativos sensibles deben quedar auditados.

> Las reglas específicas de cursos (estados, padrón, configuración de curso, desafíos) son de los **Temas 02/03/04/05**; el BackOffice las respeta como contexto y las consume vía **contratos de lectura**, pero no las implementa.
>
> **Referencia oficial:** este documento se alinea con `TUP_PIV_BE_PROPUESTA_ARQ.pdf` (Tema 12 — Backoffice) y con el PRD. La identidad/roles/auditoría corresponden al **Tema 01**; el Backoffice las consume.

Estas reglas son tomadas del PRD y constituyen restricciones de negocio del backend.

---

# 3. Objetivos del BackOffice

## 3.1 Objetivo general

Proporcionar una API segura y trazable para que ADMIN y PROFESOR puedan administrar los recursos que les corresponden sin acceder a información fuera de su ámbito.

## 3.2 Objetivos específicos

1. Administrar la configuración global de la plataforma (parámetros PAR) y el proveedor LLM (exclusiva de ADMIN).
2. Proveer reportes docentes, métricas de curso y exportación de datos.
3. Consumir identidad/roles/auditoría (Tema 01) y cohorte (Tema 02) vía contratos de lectura.
4. Mantener separación de responsabilidades mediante microservicios.
5. Evitar que un servicio pueda modificar directamente la base de datos de otro.
6. Registrar (vía auditoría del Tema 01) las operaciones administrativas sensibles.
7. Aplicar autorización en el gateway y en cada microservicio (validar ≠ autorizar).
8. Mantener aislamiento lógico de la información.
9. Permitir evolucionar cada dominio independientemente.
10. Proporcionar una base técnica preparada para futuras integraciones.

---

# 4. Requerimientos Funcionales

> **Convención:** Los identificadores `RF-...` deben mantenerse estables para poder trazarlos hacia endpoints, código y pruebas.

---

## 4.1 Identidad, roles y autorización — **consumido del Tema 01**

> Según `TUP_PIV_BE_PROPUESTA_ARQ.pdf`, el dominio de **identidad, roles, 2FA, sesión, auditoría y retención pertenece al Tema 01 (Identidad y Usuarios)**. El Backoffice lo **consume** para operar (autenticar a sus ADMIN/PROFESOR y autorizar sus endpoints).

El Backoffice **respeta** estas reglas de plataforma (tomadas del PRD) pero **no las implementa**:

- Todos los ADMIN tienen los mismos permisos (RF-ROL-01).
- Un ADMIN no puede auto-eliminarse (RF-ROL-02).
- ADMIN solo se crea/elimina por otro ADMIN (RF-ROL-03).
- Break-glass server-only con secreto, cambio de contraseña y auditoría (RF-ROL-04).
- Protección del último ADMIN, incondicional (RF-ROL-05).
- Baja reforzada: contraseña + 2FA + confirmación (RF-ROL-06).

**Cómo consume el Backoffice:** al recibir una solicitud administrativa, el gateway valida el token (Tema 01) y propaga el contexto de usuario; el microservicio Backoffice consulta a Tema 01 (por el gateway) la decisión de autorización por rol/permiso y, para reportes, la pertenencia a la cohorte (Tema 02). *Validar ≠ autorizar*: la autorización final la toma el servicio dueño de la regla.

---

## 4.2 Configuración global

### RF-CFG-01 — Configuración global

El BackOffice deberá permitir a ADMIN administrar las configuraciones globales de la plataforma.

### RF-CFG-02 — Configuración por curso

La configuración correspondiente a un curso deberá poder ser administrada por el PROFESOR creador del curso.

El BackOffice deberá respetar la separación entre configuración global y configuración de curso.

### RF-CFG-03 — Configuración de usuario

El sistema deberá contemplar configuraciones a nivel usuario cuando correspondan al dominio de usuario.

### RF-CFG-04 — Parámetros económicos globales

ADMIN podrá administrar los parámetros de la economía de gamificación.

Entre ellos se encuentran:

| Parámetro | Valor de referencia |
|---|---:|
| XP base por dificultad | 100 / 250 / 500 |
| XP de desafíos personalizados | 10 / 20 / 30 |
| Monedas por desafío obligatorio/opcional | 100 / 50 |
| Variación por calidad/tiempo | ±15% |
| Bonus/penalidad por uso de IA | ±20% |
| Precio de una vida | 300 |
| Precio de equipamiento | 500 |
| XP por defecto para desbloqueo | 500 |
| Curva de niveles | definida por PAR-09 |
| Muestreo de auditoría de IA | 10% |
| Umbral anti-fuga | 70% |
| Vidas iniciales/máximo | 3 / 3 |
| Máximo de reintentos | 3 |
| Tolerancia de calibración | ±5 promedio / ±10 por dimensión |
| Recalibración | mensual / ante cambio de modelo |
| Retención académica | 5 años |
| Preaviso de retención | 90 días |
| Mínimo de respuestas para encuesta | 5 |

Estos valores deben tratarse como **configuración**, no como constantes hardcodeadas.

### PAR-19..PAR-24 — Candidatos deducidos de la especificación (a validar con la cátedra)

> **Nota importante:** el documento del profe asigna el **registro PAR-01..PAR-24** (Lámina 6), pero la tabla del PRD solo define hasta **PAR-18** (economía). El PRD (nota a `RF-CFG-04`) aclara que *"los parámetros operativos de plataforma se completan en LL"*. Los siguientes **6 candidatos** fueron **deducidos** de la especificación (magic numbers de los temas consumidores + Lámina 6) y **NO son oficiales**: se presentan **a validar con la cátedra**. Los valores marcados como *propuesto* no tienen número oficial en la documentación.

| PAR | Clave técnica (sugerida) | Tipo | Valor de referencia | Cita de la especificación | Consume |
|---|---|---|---|---|---|
| **PAR-19** | `late_submission_penalty_pct` | FLOAT | 30% (`0.30`) | "Entrega tardía con penalidad del 30% en ventana de 48 h" | **T05** (Prácticos) |
| **PAR-20** | `late_submission_window_hours` | INTEGER | 48 h | "…penalidad del 30% en ventana de 48 h" | **T03 / T05** (Desafíos) |
| **PAR-21** | `event_multiplier_cap` | FLOAT | 3x (`3.0`) | "Multiplicador de eventos con techo de 3x" | **T08 / T10** (Banco / Gamificación) |
| **PAR-22** | `llm_custom_challenges_weekly_limit` | INTEGER | *propuesto*: 5/semana *(sin número oficial)* | "Desafíos personalizados por LLM… Límite semanal de generación" | **T03 / T07** |
| **PAR-23** | `reporting_cache_freshness_minutes` | INTEGER | 15 min | "Frescura máxima de 15 minutos en los datos" | **T12** (Reporting) |
| **PAR-24** | `session_inactivity_timeout_minutes` | INTEGER | *propuesto*: 30 min *(sin número oficial; dueño real: T01)* | "Parámetros operativos de plataforma (política de sesiones)" | **T01** (Identity) |

**Por qué como candidatos:** evita *magic numbers* en los microservicios (Lámina 6), permite al ADMIN ajustar reglas operativas sin recompilar, y el registro genérico (jsonb) los soporta sin migraciones. **Coordinación:** PAR-24 (sesión) es operativa de **T01** → confirmar si es PAR del Backoffice o configuración propia de T01.

### RF-CFG-05 — Separación de ámbitos de configuración

El backend deberá impedir que un PROFESOR modifique parámetros globales reservados a ADMIN.

ADMIN define valores globales de economía y otros parámetros globales.

PROFESOR define decisiones propias de su curso, como:

- Dificultad de desafíos.
- Obligatoriedad.
- Reintentos permitidos.
- Umbrales de XP de secciones.
- Set de niveles utilizado.

### RF-CFG-06 — Aplicación hacia adelante

Los cambios de parámetros globales deberán aplicarse únicamente hacia adelante.

Los desafíos ya resueltos no deberán recalcular su XP o monedas históricas debido a un cambio de configuración.

---

## 4.3 Usuarios y padrón — **consumido de los Temas 01 y 02**

> El dominio de usuarios (onboarding, validación de legajo, **avatar**, perfil) pertenece al **Tema 01 (Identidad y Usuarios)**; el padrón y la matrícula de la cohorte pertenecen al **Tema 02 (Cursos y Matrícula)**. El Backoffice **no los implementa**.

El Backoffice solo necesita, vía **contratos de lectura**:

- Identidad básica, rol y estado de la cuenta del ADMIN/PROFESOR que opera (Tema 01).
- La **pertenencia del PROFESOR a una cohorte** para autorizar reportes/métricas (Tema 02, "matrícula").
- Eventos de padrón/matrícula (`RosterUpdated` / matrícula) para métricas de curso.

### RF-USR-06 — Validación de ámbito

El backend verifica que cada actor solo acceda a la información de su ámbito (ADMIN global; PROFESOR solo sus cohortes, validado contra matrícula del Tema 02).

---

## 4.4 Gestión de proveedores de modelo (exclusiva de ADMIN)

> Área oficial del BackOffice (punto clave de los docentes). El servicio de IA (otro equipo) **consume** esta configuración; el BackOffice la administra.

### RF-IA-ADM-01 — Alta, sustitución y baja de proveedores/modelos (RF-IA-35)

Potestad exclusiva del ADMIN, auditada (quién, cuándo, qué modelo, para qué función).

### RF-IA-ADM-02 — Asignación modelo ↔ función (RF-IA-23/24)

Configuración global: qué modelo se usa para cada función (tutor, evaluador, moderador, generador, RAG). No configurable por PROFESOR/curso.

### RF-IA-ADM-03 — Evaluador único activo (RF-IA-25)

La función evaluador de uso de IA admite **un único modelo activo**; se registra `model_id` + `model_version` junto a `rubric_version`.

### RF-IA-ADM-04 — Cambio de modelo evaluador (RF-IA-28)

El ADMIN puede cambiar el modelo evaluador en cualquier momento, sin bloqueo por período académico, sujeto a calibración.

### RF-IA-ADM-05 — Golden set base y calibración (RF-IA-30/31)

- Golden set base a nivel plataforma, versionado junto a la rúbrica.
- Habilitación de un modelo evaluador exige puntuar el golden set completo dentro de tolerancia (PAR-14).

### RF-IA-ADM-06 — Detección de deriva (RF-IA-32)

Re-calibración periódica y ante cambio de versión del proveedor; si cae fuera de tolerancia, alerta al ADMIN.

### RF-IA-ADM-07 — Trazabilidad de cohortes (RF-IA-33)

Si el evaluador cambia con curso activo, los desafíos evaluados con el modelo anterior quedan marcados y se señaliza al PROFESOR.

---

## 4.5 Reportes docentes, métricas de curso y exportación

> Área oficial del Backoffice según `TUP_PIV_BE_PROPUESTA_ARQ.pdf`: reportes docentes, exportación, KPIs CSAT, panel del profesor, alertas.

### RF-RPT-01 — Reportes docentes

PROFESOR podrá consultar reportes de sus cursos; ADMIN podrá consultar el consolidado de plataforma y desglose por curso.

### RF-RPT-02 — Panel de métricas de curso

El BackOffice deberá exponer métricas por cohorte: satisfacción (KPIs de encuesta agregados y anónimos, **CSAT 5 estrellas**), engagement (alumnos activos semanales, ritmo de resolución), aprobación/abandono.

### RF-RPT-03 — Panel del profesor con indicador de alumno en riesgo

El panel del profesor deberá incluir un **indicador de alumno en riesgo** (para más adelante, previsto en el modelo y contrato).

### RF-RPT-04 — Exportación de datos

El BackOffice deberá permitir exportar resúmenes administrativos (ej. resumen de cierre de curso, reportes por curso) en formato descargable (CSV/PDF).

### RF-RPT-05 — Alertas configurables

El BackOffice deberá soportar **alertas configurables** (para más adelante) sobre los datos de lectura.

### RF-RPT-06 — Frescura de datos ≤ 15 minutos

Los datos que el Backoffice presenta deben tener una **frescura máxima de 15 minutos** (los read models se actualizan vía eventos con ese límite).

### RF-RPT-07 — Sin comparación entre docentes

El reporting **no** permite comparar resultados entre docentes (regla del documento): cada docente ve solo sus cohortes y el ADMIN ve agregados sin ranking docente.

### RF-RPT-08 — No exposición de datos fuera de ámbito

El reporting aplica las mismas reglas de autorización que los demás servicios.

### RF-RPT-09 — Encuestas anónimas

Al consumir resultados de encuestas, debe respetarse el anonimato 100% (RF-ENC-04/12): sin operación que reconstruya autor ↔ respuesta.

### RF-RPT-10 — Contratos de lectura con los seis temas

El Backoffice depende de **contratos de lectura** con los temas que le proveen datos (02 Cursos, 04 Teóricos/Encuestas, 05 Prácticos, 07 Evaluación LLM, 08 Banco, 10 Roadmap). Sin esos contratos acordados en el sprint 1, el Backoffice no tiene nada demostrable (es consumidor puro).

---

## 4.6 Auditoría — **consumida del Tema 01**

> La **persistencia y consulta de auditoría** pertenecen al **Tema 01 (Identidad y Usuarios)**. El Backoffice **emite** los eventos de auditoría de sus operaciones administrativas (o los provoca), y Tema 01 los persiste; el Backoffice puede **consultar** auditoría vía contrato de lectura, pero **no implementa** el registro.

### RF-AUD-01 — Registro de acciones administrativas

Las operaciones administrativas sensibles del Backoffice (cambios de parámetros, gestión del proveedor LLM) quedan **auditadas por el Tema 01**.

### RF-AUD-02 — Datos mínimos de auditoría

Los eventos de auditoría que el Backoffice emite contienen, como mínimo: evento, actor, rol, fecha/hora, operación, recurso, resultado, motivo cuando aplique y correlation ID (el contrato lo define Tema 01).

### RF-AUD-03 — Acciones que el Backoffice debe auditar

Como mínimo: cambios de configuración global y cambios de proveedores de modelo (RF-IA-35).

---
- Overrides administrativos permitidos por el PRD.

### RF-AUD-04 — Inmutabilidad lógica

Los registros de auditoría no deberán modificarse desde las APIs administrativas comunes.

---

## 4.7 Retención — **consumida del Tema 01**

> La **retención** (5 años configurable, sin purga automática, decisión de ADMIN) pertenece al **Tema 01 (Identidad y Usuarios)**. El Backoffice **no la implementa**: la respeta como política de plataforma y la consume cuando corresponde.

### RF-RET-01 — Conservación

Los datos académicos se conservan por el plazo definido por el PRD (5 años configurable); esto lo aplica el Tema 01.

### RF-RET-02 — Vencimiento no destructivo

El vencimiento del plazo no produce purga automática: el registro pasa a pendiente de decisión (Tema 01).

### RF-RET-03/04 — Decisión administrativa

La decisión de extender/anonimizar y su auditoría la resuelve el ADMIN a través del Tema 01.

---

## 4.8 Consultas administrativas y reporting

> Consolidado con §4.5 (reportes docentes, métricas y exportación). Este apartado cubre las consultas transversales de supervisión.

### RF-REP-01 — Información administrativa

El BackOffice deberá proporcionar consultas necesarias para que ADMIN pueda supervisar el estado general de la plataforma (configuración, proveedores de modelo, métricas globales).

### RF-REP-02 — Información por curso (reportes docentes)

PROFESOR podrá consultar reportes correspondientes a sus cohortes; ADMIN, el consolidado de plataforma y desglose por curso (ver RF-RPT-01/02).

### RF-REP-03 — No exposición de datos fuera de ámbito

El servicio de reporting deberá aplicar las mismas reglas de autorización que los demás servicios.

### RF-REP-04 — Encuestas

Cuando el BackOffice consuma resultados de encuestas, deberá respetar que las respuestas son 100% anónimas (RF-ENC-04/12).

No deberá existir ninguna operación administrativa que permita reconstruir la relación entre una respuesta y su autor.

---

# 5. Requerimientos No Funcionales

> Los siguientes requisitos derivan de los NFR del PRD. Cuando el PRD deja una decisión para Low Level Design, se indica explícitamente como una decisión técnica de este documento y no como una exigencia funcional del PRD.

---

## RNF-01 — Seguridad

Toda operación administrativa deberá requerir autenticación.

Las operaciones deberán estar protegidas mediante autorización basada en rol y, cuando corresponda, pertenencia al recurso.

---

## RNF-02 — Autenticación

La plataforma utilizará:

- Usuario.
- Contraseña.
- Segundo factor (2FA).

El 2FA será obligatorio para los roles definidos por el PRD.

La vinculación con GitHub no será utilizada como mecanismo de autenticación.

---

## RNF-03 — Autorización

La autorización deberá aplicarse en dos niveles:

1. API Gateway.
2. Microservicio propietario del recurso.

El Gateway no será considerado una frontera de seguridad suficiente.

Cada microservicio deberá volver a validar:

- Identidad.
- Rol.
- Permisos.
- Alcance del recurso.

---

## RNF-04 — Aislamiento de datos

Cada microservicio será propietario de su propia base de datos.

No se permitirá que un microservicio consulte directamente tablas de otro.

La comunicación entre dominios se realizará mediante:

- APIs.
- Eventos.

---

## RNF-05 — Escalabilidad

La arquitectura deberá soportar al menos el objetivo establecido por el PRD:

**120 sesiones concurrentes**, incluyendo invocaciones concurrentes de IA en el escenario de carga general.

Aunque la IA no sea responsabilidad del BackOffice, la arquitectura deberá evitar convertirse en un cuello de botella para las operaciones administrativas.

---

## RNF-06 — Resiliencia

La caída de un servicio no deberá provocar indisponibilidad innecesaria de los demás servicios.

Las comunicaciones síncronas deberán implementar:

- Timeouts.
- Manejo de errores.
- Retry controlado cuando corresponda.
- Circuit breaker cuando sea necesario.

Las operaciones asíncronas deberán poder reintentarse sin duplicar efectos.

---

## RNF-07 — Consistencia e idempotencia

Las operaciones administrativas críticas deberán ser idempotentes cuando sea técnicamente posible.

Los consumidores de eventos deberán soportar mensajes duplicados.

Se utilizará un identificador único de evento para evitar procesamiento repetido.

---

## RNF-08 — Trazabilidad

Cada request deberá poseer un `Correlation-Id`.

Este identificador deberá propagarse:

`Gateway → Servicio → Evento → Servicio consumidor`

Esto permitirá reconstruir el recorrido de una operación distribuida.

---

## RNF-09 — Auditoría

Las acciones administrativas sensibles deberán quedar registradas.

Los logs técnicos y la auditoría de negocio deberán mantenerse conceptualmente separados:

- **Logs:** diagnóstico técnico.
- **Auditoría:** evidencia de acciones de negocio.

---

## RNF-10 — Borrado lógico

Las entidades de producción académica deberán utilizar borrado lógico cuando así lo establezca el PRD.

No deberá utilizarse un `DELETE` físico como mecanismo normal para eliminar información académica.

---

## RNF-11 — Retención

La retención académica tendrá como referencia:

- 5 años desde el cierre del curso.
- Preaviso de 90 días.
- Sin purga automática.
- Decisión explícita de ADMIN.
- Auditoría de la decisión.

---

## RNF-12 — Privacidad

La arquitectura deberá evitar exponer información personal a servicios que no la necesiten.

Los datos de encuestas deberán mantenerse anónimos por diseño.

No se implementará una relación técnica que permita reconstruir autor ↔ respuesta.

---

## RNF-13 — Internacionalización

El MVP se implementará en español.

La arquitectura deberá evitar acoplar reglas de negocio al idioma para facilitar futuras extensiones.

---

## RNF-14 — Plataforma

El producto se define para web responsive.

El backend deberá exponer APIs HTTP/REST documentadas para el cliente BackOffice.

---

## RNF-15 — Mantenibilidad

El código deberá organizarse siguiendo separación por capas:

- API.
- Aplicación.
- Dominio.
- Infraestructura.

Se evitará colocar reglas de negocio en controllers.

---

## RNF-16 — Observabilidad

Cada microservicio deberá generar:

- Logs estructurados.
- Métricas básicas.
- Health checks.
- Correlation ID.

---

## RNF-17 — Configuración

Las credenciales y secretos no deberán almacenarse en el repositorio.

Se utilizarán variables de entorno y/o un mecanismo centralizado de configuración.

---

## RNF-18 — Documentación de API

Las APIs deberán documentarse mediante OpenAPI/Swagger.

Cada endpoint deberá especificar:

- Método HTTP.
- Ruta.
- Parámetros.
- Body.
- Respuestas.
- Errores.
- Reglas de autorización.

---

# 6. Arquitectura propuesta

## 6.1 Estilo arquitectónico

Se utilizará una:

> **Arquitectura de Microservicios orientada a dominios para el Backend del BackOffice.**

La decisión de utilizar microservicios se toma también por el requisito académico del proyecto, pero se evitará crear un microservicio por cada entidad.

La división se realizará por **bounded contexts / responsabilidades de negocio**.

---

## 6.2 Stack tecnológico decidido

El stack fue decidido por el equipo en base a la experiencia previa con Java y Spring:

| Capa | Tecnología |
|---|---|
| Lenguaje | Java 21 (LTS) |
| Framework | Spring Boot 3 |
| Build | Maven (multi-módulo) |
| API Gateway | Spring Cloud Gateway |
| Service Discovery | Spring Cloud Netflix **Eureka** (Consul queda como alternativa documentada) |
| Config centralizada | Spring Cloud Config Server |
| Mensajería | Kafka (Spring for Apache Kafka) + patrón Outbox |
| Persistencia | JPA/Hibernate |
| Base de datos | **PostgreSQL (recomendada, pendiente de confirmación)** — ver nota abajo |
| Documentación API | springdoc / OpenAPI 3 |
| Seguridad | Spring Security + JWT (jjwt) + 2FA (TOTP) |
| Validación | Jakarta Validation (Bean Validation) |
| Pruebas | JUnit 5, Mockito, Testcontainers, Spring Boot Test |
| Testing de contratos | Spring Cloud Contract o Pact |

> **Nota sobre base de datos:** se recomienda **PostgreSQL** por: licencia open source sin restricciones, combinación Hibernate + PostgreSQL más probada y documentada, soporte nativo de JSONB (útil para auditoría y payloads de eventos), imagen Docker liviana para levantar 5 bases en local, y transacciones/locking sólidos para las reglas críticas (último ADMIN, reservas de monedas). SQL Server también es compatible con Spring (driver oficial de Microsoft) si algún integrante ya lo tiene disponible. **Decisión técnica recomendada, pendiente de confirmación con el equipo.**

> **Nota sobre mensajería:** se elige **Kafka** como broker de eventos desde el planteamiento inicial (ver ADR-003); Kafka queda documentado como **alternativa** (por ejemplo si en la coordinación con otros equipos se prefiere un broker más liviano). Los patrones de consumo no cambian: se mantienen **Outbox + at-least-once + idempotencia**. Kafka se elige por su capacidad de **replay/histórico** (clave para reconstruir read models de Reporting y para la auditoría inmutable RF-AUD-04), por **orden garantizado por partición** (`courseId`/tenant) y por su escalabilidad horizontal.

---

# 7. Componentes principales

> Según `TUP_PIV_BE_PROPUESTA_ARQ.pdf`, el **API Gateway es de plataforma** (asignado al Tema 01 como extra). El Backoffice expone **2 servicios propietarios** y **consume** identidad/autorización (Tema 01), cohorte (Tema 02) y lecturas de los temas 02/04/05/07/08/10.

```text
                          ┌───────────────────────┐
                          │   BackOffice Frontend  │
                          │   (por definir)       │
                          └───────────┬───────────┘
                                      │ HTTPS
                                      ▼
                    ┌──────────────────────────────────┐
                    │  API Gateway de PLATAFORMA (T01) │
                    │  única puerta · sync por acá     │
                    └───────────┬──────────┬──────────┘
                                │          │
               ┌────────────────┘          └────────────────┐
               │                                             │
               ▼                                             ▼
      ┌────────────────┐                          ┌────────────────┐
      │ Administration │                          │  Reporting &   │
      │ & Configuration│                          │   Analytics    │
      │    Service     │                          │    Service     │
      └───────┬────────┘                          └───────┬────────┘
              │                                          │
              ▼                                          ▼
         ┌──────────┐                              ┌──────────┐
         │  PostgreSQL │                           │  PostgreSQL │
         └──────────┘                              └──────────┘

   Consume: Tema 01 (identidad/roles/auditoría) · Tema 02 (cohorte) · lecturas 02/04/05/07/08/10
   (todo a través del gateway; asincrónico por Kafka)

                          ┌────────────────┐
                          │     Kafka      │
                          │ Event Broker   │
                          └────────────────┘

                          ┌────────────────┐
                          │     Eureka     │
                          │   Discovery    │
                          └────────────────┘
```

---

# 8. Microservicios

El Backoffice tiene **2 servicios propietarios** (los únicos que implementa). Identidad, roles, 2FA, auditoría y retención los **consume del Tema 01**; la cohorte del **Tema 02**.

## 8.1 Administration & Configuration Service

### Responsabilidad

Administrar la configuración global de la plataforma, incluida la **gestión de proveedores y modelos de IA** (exclusiva de ADMIN).

### Funciones

- Registro de parámetros **PAR-01..PAR-24** (base PRD PAR-01..18; registro genérico/extensible) y parámetros operativos configurables.
- **Gestión de proveedores de LLM** (RF-IA-35): alta, sustitución, baja, auditada.
- **Asignación modelo ↔ función** (RF-IA-23/24).
- **Configuración del evaluador** (RF-IA-25/28): modelo único activo, cambio con calibración.
- **Golden set base y calibración a nivel plataforma** (RF-IA-30/31).
- **Detección de deriva** (RF-IA-32): re-calibración periódica y alerta.
- Versionado de configuración y aplicación de cambios hacia adelante (RF-CFG-06).
- Autorización de sus endpoints (consume roles del Tema 01).

### Base de datos

`administration_db`

### No debe hacer

- Implementar llamadas a los LLM (eso es del Tema 07; consume la configuración).
- Implementar identidad/auth/roles/auditoría (Tema 01), cursos/matrícula (Tema 02) ni otros temas.

---

## 8.2 Reporting & Analytics Service

### Responsabilidad

Proporcionar **reportes docentes**, **panel del profesor**, **métricas de curso**, **exportación de datos** y **alertas** para el Backoffice. **Consumidor puro**: no es dueño de la información operacional.

### Funciones

- Reportes docentes por cohorte (PROFESOR) y consolidado de plataforma (ADMIN).
- Panel del profesor con **indicador de alumno en riesgo**.
- Métricas de cohorte: satisfacción (**KPIs CSAT 5★**, encuestas agregadas/anónimas), engagement (activos semanales, ritmo), aprobación/abandono.
- Exportación de datos (resúmenes administrativos, reportes CSV/PDF).
- **Alertas configurables**; **frescura ≤ 15 minutos** en los datos de lectura; **sin comparación entre docentes**.
- Consumo de lecturas de los temas 02/04/05/07/08/10 (eventos/APIs a través del gateway) para construir read models.

### Base de datos

`reporting_db`

El Reporting & Analytics no será dueño de la información operacional original. Mantendrá datos derivados/read models, reconstruibles por contratos de lectura (REST).

---

## 8.3 Consumidos (no implementados)

| Dominio | Tema | Uso del Backoffice |
|---|---|---|
| Identidad, auth, 2FA, roles, sesión, auditoría, retención | **Tema 01** | Consume para autenticar/autorizar y auditar sus operaciones |
| Curso-cohorte, matrícula, padrón | **Tema 02** | Consume la cohorte como clave `course_id` y la pertenencia docente |
| Desafíos, teóricos, encuestas, prácticas, sandbox, evaluación LLM, banco, mercado, roadmap, social | Temas 03-11 | Solo **lectura** (02/04/05/07/08/10) para reportes/métricas |

---

# 9. API Gateway

Se utilizará un **API Gateway** como punto de entrada único del BackOffice.

### Responsabilidades

- Routing.
- Terminación HTTPS.
- Autenticación inicial.
- Validación de JWT.
- Rate limiting.
- Correlation ID.
- CORS.
- Manejo uniforme de errores.
- Health routing.
- Documentación/agregación de APIs cuando corresponda.

### Importante

El Gateway **no reemplaza la autorización interna**.

Ejemplo:

```text
ADMIN
  │
  ▼
API Gateway (de plataforma, Tema 01)
  │ JWT válido
  ▼
Administration & Configuration Service
  │
  ├── ¿Rol/permiso? (consulta a Tema 01)
  ├── ¿Regla de negocio permite operación?
  └── (si necesita otro servicio, sale y vuelve por el gateway)
```

## 9.1 Comunicación entre servicios por el gateway

Regla no negociable del documento del profe: **no hay comunicación directa entre microservicios**; toda llamada síncrona entre servicios **vuelve a pasar por el gateway** (balanceo, validación centralizada y traza). Lo asincrónico viaja por el bus de eventos. El Backoffice (Administration, Reporting) se comunica con Tema 01/02 y demás temas **por el gateway** (síncrono) o por eventos (asíncrono).

## 9.2 Rate limiting y 429

Para evitar saturación de los endpoints administrativos y el abuso (p. ej. fuerza bruta en login), el Gateway aplica **rate limiting**:

- **Tecnología:** Spring Cloud Gateway con token bucket — **Bucket4j** (in-memory, por instancia) o **Redis `RequestRateLimiter`** (distribuido, si hay varias instancias).
- **Umbrales por endpoint y rol**, con atención especial a:
  - `/api/auth/login` y `/api/auth/2fa/verify` (prevención de fuerza bruta).
  - `/api/audit` (consultas pesadas).
  - Operaciones administrativas críticas (PUT de configuración, baja de ADMIN).
- **Respuesta:** `429 Too Many Requests` con header **`Retry-After`** y el formato de error uniforme (§29).
- **Nota multi-instancia:** el bucket in-memory limita por instancia; con N instancias el límite efectivo se multiplica. Si eso importa, se usa Redis.

**Complemento server-side — Idempotency Keys:** las operaciones administrativas críticas del Backoffice (PUT de configuración, gestión de proveedores de modelo) aceptan una `Idempotency-Key`. Si el backend ya procesó esa clave, responde con el resultado original en vez de re-ejecutar. Es la red de seguridad del backend frente a peticiones duplicadas (el front también aplica single-flight, ver plan frontend).

---

# 10. Service Discovery y configuración

## 10.1 Eureka

Se propone utilizar **Spring Cloud Netflix Eureka** para:

- Service Discovery.
- Health checks (reenvío de estado de cada instancia).
- Registro de instancias.
- Balanceo de carga del lado del cliente (Spring Cloud LoadBalancer).

> Alternativa documentada: **Consul** (Hashicorp) ofrece además un KV store para configuración distribuida. Se mantiene como opción si el equipo prefiere consolidar discovery y configuración en un solo componente; la elección de Eureka se justifica por ser el estándar del ecosistema Spring Cloud y por la experiencia previa del equipo.

Ejemplo:

```text
course-service
 ├── instance-1
 └── instance-2

configuration-service
 └── instance-1
```

El Gateway y los servicios podrán resolver dinámicamente la ubicación de las instancias.

## 10.2 Config Server

La configuración centralizada se implementará con **Spring Cloud Config Server**:

- URLs internas entre servicios.
- Feature flags.
- Parámetros no secretos.
- Configuración de infraestructura.
- Perfiles por ambiente (`development`, `testing`, `production`).

Los secretos **nunca** se almacenan en el repositorio de configuración: se inyectan mediante variables de entorno o secret manager en tiempo de ejecución.

---

# 11. Comunicación entre microservicios

Se utilizarán dos mecanismos.

## 11.1 Comunicación síncrona

HTTP/REST se utilizará cuando se necesite una respuesta inmediata.

Ejemplo:

```text
BackOffice
    │
    ▼
API Gateway
    │
    ▼
Administration & Configuration Service
    │
    ├── consulta Identity & Access (¿rol?)
    │
    └── respuesta
    │
    ▼
BackOffice
```

Debe utilizarse para:

- Consultas.
- Validaciones necesarias para completar una operación.
- Operaciones donde el resultado sea requerido inmediatamente.

---

## 11.2 Comunicación asíncrona

Kafka se utilizará para eventos de dominio, con **topic por dominio + consumer group por servicio** y **caché local con TTL 10 min** en los consumidores de parámetros.

Ejemplo de **publicación** (BackOffice):

```text
Administration & Configuration Service
      │
      │ GlobalConfigurationChanged
      ▼
   Kafka (topic: administration.events)
      │
      ├──────────────► Tema 01 · auditoría (consumer group: audit)
      │
      └──────────────► Gamification Service (cross-team)
```

Ejemplo de **consumo** (reportes y métricas):

```text
Cursos Service (equipo Cursos)
      │
      │ CourseArchived / RosterUpdated
      ▼
   Kafka (topic: course.events)
      │
      ▼
   Reporting & Analytics Service (consumer group: reporting)
```

Ventajas:

- Menor acoplamiento.
- Procesamiento independiente.
- Reintentos.
- Propagación de cambios.
- Construcción de read models.

---

# 12. Eventos de dominio

Ejemplos:

```text
UserCreated
UserRoleChanged
AdminDeleted
AdminRecoveryExecuted

GlobalConfigurationChanged

CourseCreated
CourseUpdated
CourseActivated
CourseArchived

RosterUpdated

ChallengeCreated
ChallengeUpdated

RetentionDecisionCreated
DataAnonymized
```

Los eventos deberán incluir:

```json
{
  "eventId": "uuid",
  "eventType": "CourseArchived",
  "occurredAt": "2026-08-28T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "source": "course-service",
  "payload": {}
}
```

### Topics y particiones (convención Kafka)

| Topic | Eventos | Rol BackOffice | Consumer group por servicio |
|---|---|---|---|
| `identity.events` | AdminCreated, AdminDeleted, AdminRecoveryExecuted, RoleChanged | Publica | `audit`, `reporting`… |
| `administration.events` | GlobalConfigurationChanged, ModelProviderChanged, ModelFunctionChanged | Publica | `gamification`, `challenges`, `bank`, `roadmap`… |
| `audit.events` | eventos de auditoría (RF-AUD-*) | Publica | `audit` |
| `retention.events` | RetentionDecisionCreated, DataAnonymized | Publica | `audit`, `reporting`… |
| `course.events` | CourseCreated, CourseActivated, CourseArchived, RosterUpdated | **Consume** | `reporting` |
| `gamification.events` / `ranking.events` / `survey.events` | eventos de otros equipos | **Consume** | `reporting` |

Cada **consumer group** pertenece a un consumidor (un servicio). **Idempotencia por `event_id` y por `version`** (el consumidor descarta `v ≤ local`). Los **read models de Reporting se reconstruyen vía contratos de lectura REST** (no dependen del historial del broker). Los consumidores de parámetros usan **caché local con TTL 10 min** que el evento invalida antes (respaldo ante caída del Backoffice).

### Payloads concretos de eventos cross-team

```jsonc
// GlobalConfigurationChanged (Administration → Gamification, RF-CFG-04)
{
  "eventId": "uuid",
  "eventType": "GlobalConfigurationChanged",
  "occurredAt": "2026-08-28T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "source": "administration-service",
  "payload": {
    "key": "PAR-01",
    "value": {"BASICO": 100, "MEDIO": 250, "AVANZADO": 500},
    "version": 7
  }
}
```

```jsonc
// ModelProviderChanged (Administration → AI Service, RF-IA-35)
{
  "eventId": "uuid",
  "eventType": "ModelProviderChanged",
  "occurredAt": "2026-08-28T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "source": "administration-service",
  "payload": {
    "function": "EVALUATOR",
    "modelId": "model-42",
    "modelVersion": "gpt-5.2",
    "rubricVersion": "r3",
    "status": "ACTIVE"
  }
}
```

```jsonc
// CourseArchived (Cursos → Reporting + Audit, consumido)
{
  "eventId": "uuid",
  "eventType": "CourseArchived",
  "occurredAt": "2026-08-28T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "source": "course-service",       // equipo Cursos
  "payload": {
    "courseId": "course-10",
    "archivedAt": "2026-08-28T12:00:00Z",
    "academicSummaryId": "summary-77"
  }
}
```

```jsonc
// RosterUpdated (Cursos → Reporting, consumido)
{
  "eventId": "uuid",
  "eventType": "RosterUpdated",
  "occurredAt": "2026-08-28T12:00:00Z",
  "correlationId": "uuid",
  "actorId": "uuid",
  "source": "course-service",
  "payload": {
    "courseId": "course-10",
    "action": "BULK_IMPORT",
    "added": 40,
    "updated": 3,
    "errors": 2
  }
}
```

---

# 13. Patrón Outbox

Para evitar inconsistencias entre una transacción de base de datos y la publicación de eventos, se propone utilizar **Transactional Outbox**.

```text
                ┌──────────────────────────┐
                │ Administration & Config. │
                └─────────┬────────────────┘
                          │
                ┌─────────▼──────────┐
                │      Database      │
                │                    │
                │ GlobalParameter    │
                │ OutboxMessage      │
                └─────────┬──────────┘
                          │
                     Publisher
                          │
                          ▼
                    ┌───────────┐
                    │   Kafka   │
                    └───────────┘
```

### Motivo

Sin Outbox podría ocurrir:

1. Se actualiza un parámetro global.
2. La transacción confirma.
3. Falla Kafka (broker no disponible).
4. El cambio existe pero ningún otro servicio recibe el evento.

Con Outbox, la modificación y el evento se guardan en la misma transacción.

---

# 14. Idempotencia

Los consumidores deberán evitar procesar dos veces el mismo evento.

Se podrá utilizar:

```text
ProcessedEvent
----------------
event_id
consumer
processed_at
```

Antes de procesar:

```text
¿event_id ya procesado?
       │
   ┌───┴───┐
   │       │
  Sí      No
   │       │
Ignorar   Procesar
           │
           ▼
       Registrar ID
```

---

# 15. Modelo de datos

## 15.1 Regla Database per Service

Cada servicio tendrá una base independiente. El Backoffice tiene **2 bases propias**; identidad (Tema 01) y auditoría (Tema 01) viven en las bases de ese tema, no acá.

```text
Administration & Configuration Service
    └── administration_db

Reporting & Analytics Service
    └── reporting_db
```

No habrá Foreign Keys entre bases de microservicios.

Las relaciones entre dominios se representarán mediante IDs.

---

# 16. Modelo de datos detallado

Modelo por servicio, con campos y tipos para las entidades JPA. Las relaciones entre dominios se representan con IDs (sin Foreign Keys entre bases). **Solo los 2 servicios propietarios del Backoffice**; identidad/roles/auditoría (Tema 01), cohorte (Tema 02) y los demás temas no se modelan acá (se consumen/leen).

## 16.1 administration_db — configuración y proveedores de modelo

### GlobalParameter

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID | PK |
| key | varchar(20) | PAR-01..PAR-24 (base PRD PAR-01..18; registro extensible) |
| value | jsonb | valor versionado (RF-CFG-06) |
| version | int | incrementa por cambio |
| updated_by | UUID | FK lógica → Tema 01 |
| updated_at | timestamp | |

### ModelProvider (RF-IA-35)

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID | PK |
| name | varchar(100) | ej. OpenAI, Anthropic |
| status | enum | ACTIVE \| RETIRED |
| created_by / created_at | UUID / timestamp | auditado |
| deleted_at | timestamp | baja lógica |

### ModelFunctionAssignment (RF-IA-23/24)

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID | PK |
| function | enum | TUTOR \| EVALUATOR \| MODERATOR \| GENERATOR \| RAG_AGENT |
| model_id | UUID | FK lógica → ModelProvider/model |
| model_version | varchar(50) | |
| updated_by / updated_at | UUID / timestamp | |

### EvaluatorConfig (RF-IA-25/28)

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID | PK |
| model_id / model_version | UUID / varchar | modelo evaluador único activo |
| rubric_version | varchar | |
| activated_at / activated_by | timestamp / UUID | |
| status | enum | PENDING_CALIBRATION \| ACTIVE \| RETIRED |

### GoldenSet (RF-IA-30/31)

| Campo | Tipo | Notas |
|---|---|---|
| id | UUID | PK |
| version | varchar | versionado junto a la rúbrica |
| entries | jsonb | transcripciones puntuadas de referencia |
| calibrated_at / calibrated_by | timestamp / UUID | |
| tolerance_ok | boolean | dentro de PAR-14 |

## 16.3 reporting_db — read models (reportes, métricas, export)

```text
CohortMetricsSnapshot        ← derivado de lecturas de los temas 02/04/05/07/08/10
  ├── course_id, satisfaction_csat, active_weekly, rhythm, approved, abandoned

TeacherReportSnapshot        ← derivado de eventos por cohorte (sin comparación entre docentes)
  ├── course_id, teacher_id, resumen agregado

AtRiskStudentSnapshot        ← panel del profesor: indicador de alumno en riesgo
  ├── course_id, student_id, risk_score

ConfigurationSnapshot        ← derivado de GlobalConfigurationChanged
  ├── key, value, version, updated_at

ModelProviderSnapshot        ← derivado de cambios de proveedores/modelos
  ├── function, model_id, model_version, status
```

Reporting no es dueño de la información operacional: reconstruye sus read models vía **contratos de lectura (REST)** desde los dominios dueños (y eventos Kafka, ADR-003). **Frescura de lectura ≤ 15 minutos.**

## 16.4 Auditoría — **persistida por el Tema 01**

La tabla `AuditEvent` (evento, actor, rol, acción, recurso, resultado, motivo, correlation ID, metadata) pertenece al **Tema 01**. El Backoffice **emite** los eventos de auditoría de sus acciones administrativas (cambios de configuración y proveedores) y puede **consultarla** vía contrato de lectura, pero no la modela ni la persiste.

---

# 17. Multi-tenancy y alcance de datos

El PRD maneja información por curso y establece diferentes ámbitos de visibilidad. El BackOffice **no es dueño de los cursos**: los consumidores (reportes, métricas) trabajan sobre `course_id` como ámbito lógico.

## 17.1 Estrategia multitenant

**Tenant = curso-cohorte (`course_id`)**. Multitenancy **lógico** (base compartida por servicio + columna tenant) + **Row-Level Security (RLS)** de PostgreSQL como refuerzo. No se usa base/esquema por tenant: los cursos son muchos y chicos, y el PRD no exige aislamiento físico.

**Dónde aplica en el Backoffice:**

| Capa | Multitenancy |
|---|---|
| Administration & Configuration (PAR, proveedores, evaluador, golden set) | **Global a propósito** (no tenant-scoped): la economía vale igual en todos los cursos. |
| Reporting & Analytics (métricas, reportes docentes, panel) | **Tenant-scoped por `course_id`**: read models acotados + pertenencia del actor. |

## 17.2 TenantContext

Componente transversal que determina el alcance por operación: `JWT → identidad/rol → course_id solicitado → validación de pertenencia (matrícula T02) → TenantContext autorizado`. **Nunca confía en el `course_id` del request**; setea `app.current_course` en la sesión de BD para RLS.

## 17.3 RLS como refuerzo

Política por tabla tenant-scoped (ej. `cohort_metrics_snapshot`) con `USING (course_id = current_setting('app.current_course')::uuid)`. La base **no devuelve filas de otros tenants** aunque falte el filtro en la query. **RLS no reemplaza la autorización** (*validar ≠ autorizar*): filtra filas dentro de un tenant ya autorizado.

## 17.4 Convención multitenancy de plataforma (para coordinar con otros equipos)

1. Tenant = curso-cohorte (`course_id`) en toda entidad tenant-scoped.
2. Clave de sesión común `app.current_course`, seteada por el TenantContext **desde el contexto validado** (nunca del request).
3. RLS en las tablas tenant-scoped de cada servicio (cada equipo sobre su propia base).
4. RLS complementa, no reemplaza, la autorización por pertenencia.
5. Caso de prueba obligatorio: `PROFESOR A → cohorte A → 200` · `PROFESOR A → cohorte B → 403`.

## 17.5 Caso ADMIN: vistas entre cursos (globales)

El **ADMIN** (rol validado en T01) tiene **dos alcances** sobre el reporting:

| Alcance | `app.current_course` | Qué ve |
|---|---|---|
| **Puntual** | `course_id` específico | Un curso en particular (mismo camino que un PROFESOR, RLS normal). |
| **Global** | `'ALL'` (centinela) | Todos los cursos: panel general, comparativas, reportes globales. |

**Mecanismo (sin apagar RLS):** el `TenantContext` setea `app.current_course = 'ALL'` **solo cuando la aplicación autorizó** al ADMIN (o a un rol/permiso explícito `REPORTS_VIEW_ALL`) a operar entre cursos. La política RLS contempla el centinela:

```sql
CREATE POLICY tenant_isolation ON cohort_metrics_snapshot
  USING (
    course_id = current_setting('app.current_course')::uuid
    OR current_setting('app.current_course') = 'ALL'
  );
```

**Reglas:**

- **Nunca se usa `BYPASSRLS`** ni se desactiva RLS: la base sigue filtrando; el "todo" es un valor explícito, no un agujero.
- **Quién puede setear `'ALL'` lo decide la aplicación** (rol ADMIN desde el token validado), **nunca el request**: un PROFESOR que intente `course_id='ALL'` recibe `403` porque el `TenantContext` no lo autoriza (la pertenencia T02 no cubre "todos").
- **Auditoría:** las lecturas con alcance global se auditan (actor, fecha, recurso) — trazabilidad RF-AUD-*.

**Pruebas de aislamiento (ampliadas):**

```text
PROFESOR A → GET /reports/courses/{cohorteA}/metrics       → 200 (su cohorte)
PROFESOR A → GET /reports/courses/{cohorteB}/metrics       → 403 (otra cohorte)
PROFESOR A → intento con alcance ALL                        → 403 (no autorizado)
ADMIN     → GET /reports/courses/{cualquierCohorte}/metrics → 200
ADMIN     → GET /reports/panel (alcance ALL)                → 200 (todos los cursos)
```

No se debe asumir que `tenant_id` identifica permanentemente a un usuario.

El backend deberá determinar el alcance mediante:

- Identidad del actor (ADMIN/PROFESOR).
- Rol.
- Pertenencia del PROFESOR al curso (para reportes por curso).
- Permisos de la operación.

Ejemplo:

```text
PROFESOR → GET /reports/courses/10/metrics

             │
             ▼

¿El profesor tiene acceso al curso 10?
             │
       ┌─────┴─────┐
      Sí           No
       │            │
    permitir      403
```

El alcance del PROFESOR sobre un curso se valida contra la **membresía real** provista por el dominio de cursos (T02, cross-team); el `course_id` del request nunca se acepta ciegamente, y RLS refuerza el aislamiento a nivel de base.

---

# 18. Seguridad de la API

## 18.1 JWT

El token contendrá información mínima necesaria:

```json
{
  "sub": "user-id",
  "role": "ADMIN",
  "jti": "token-id",
  "exp": 0000000000
}
```

No se recomienda introducir grandes cantidades de información mutable dentro del JWT.

---

## 18.2 Autorización

Ejemplo:

```text
POST /api/courses
```

Requiere:

```text
ROLE_ADMIN
OR
ROLE_PROFESOR
```

Mientras:

```text
PUT /api/configuration/global/PAR-01
```

requiere:

```text
ROLE_ADMIN
```

---

# 19. Flujo de gestión de proveedor de modelo (ADMIN)

```text
ADMIN
   │
   │ POST /api/administration/model-providers
   ▼
API Gateway
   │
   │ valida JWT + rol ADMIN
   ▼
Administration & Configuration Service
   │
   ├── valida rol ADMIN
   ├── valida datos del proveedor/modelo
   ├── registra ModelProvider/ModelFunctionAssignment
   ├── registra OutboxMessage (ModelProviderChanged)
   │
   ▼
administration_db
   │
   ▼
Kafka · topic administration.events
   │
   ├────────────► Tema 01 — auditoría (persiste)
   │
   └────────────► AI Service (equipo IA: consume la configuración)
```

---

# 20. Flujo de modificación de configuración global

```text
ADMIN
 │
 │ PUT /api/administration/parameters/PAR-01
 ▼
Gateway
 │
 ▼
Administration & Configuration Service
 │
 ├── valida ADMIN
 ├── valida valor
 ├── crea nueva versión
 ├── NO modifica datos históricos
 └── registra OutboxMessage
 │
 ▼
administration_db
  │
  ▼
Kafka · topic administration.events
  │
  ├──► Tema 01 — auditoría (persiste)
  └──► Reporting & Analytics (ConfigurationSnapshot)
```

---

# 21. Flujo de baja de ADMIN — **del Tema 01 (consumido)**

El Backoffice **no implementa** la gestión de cuentas ADMIN: es del **Tema 01**. Este flujo se documenta para entender cómo opera la plataforma (lo consume el Backoffice cuando necesita operar con cuentas administrativas).

```text
ADMIN A
   │
   │ solicita baja de ADMIN B
   ▼
Gateway (de plataforma, Tema 01)
   │
   ▼
Tema 01 — Identidad y Usuarios
   │
   ├── ¿A != B?
   ├── ¿contraseña confirmada?
   ├── ¿2FA válido?
   ├── ¿ADMIN B activo?
   ├── ¿quedará al menos un ADMIN?
   │
   ├── NO ───────► Rechazar
   │
   └── SÍ
        │
        ▼
     Baja lógica
        │
        ▼
     Outbox Event
        │
        ▼
     Kafka · topic identity.events
        │
        ▼
     Tema 01 persiste auditoría
```

---

# 22. Consumo de eventos de curso (reporting y métricas)

El BackOffice **no implementa** cursos; consume sus eventos para reportes y métricas.

```text
Cursos Service (equipo Cursos)
       │
       │ CourseArchived / RosterUpdated / CourseActivated
       ▼
    Kafka · topic course.events
       │
       ▼
 Reporting & Analytics Service
       │
       ├── actualiza CourseMetricsSnapshot
       ├── actualiza TeacherReportSnapshot
       └── (Audit, si corresponde)
       │
       ▼
    reporting_db
```

El BackOffice no implementa las responsabilidades propias de cursos, desafíos, chat, IA ni otros dominios: esos equipos publican los eventos y el BackOffice reacciona.

Estos dominios reaccionarán al evento si corresponde.

---

# 23. Estructura del repositorio

Se propone un monorepo **Maven multi-módulo** para facilitar el trabajo académico y la ejecución local.

```text
/backoffice-backend
│
├── pom.xml                      ← POM padre (gestión de dependencias y módulos)
├── mvnw / mvnw.cmd             ← Maven Wrapper
├── .env.example
├── README.md
│
├── gateway/
│   ├── pom.xml
│   ├── src/main/java/com/backoffice/gateway/
│   └── src/main/resources/application.yml
│
├── services/
│   │
│   ├── identity-access-service/
│   │   ├── pom.xml
│   │   ├── src/main/java/com/backoffice/identity/
│   │   │   ├── api/
│   │   │   ├── application/
│   │   │   ├── domain/
│   │   │   └── infrastructure/
│   │   └── src/main/resources/application.yml
│   │
│   ├── administration-service/
│   │   ├── pom.xml
│   │   ├── src/main/java/com/backoffice/administration/
│   │   │   ├── api/
│   │   │   ├── application/
│   │   │   ├── domain/
│   │   │   └── infrastructure/
│   │   └── src/main/resources/application.yml
│   │
│   ├── reporting-service/
│   │   ├── pom.xml
│   │   └── src/main/java/com/backoffice/reporting/
│   │
│   └── audit-service/
│       ├── pom.xml
│       └── src/main/java/com/backoffice/audit/
│
├── building-blocks/
│   ├── contracts/              ← DTOs compartidos / contratos de eventos
│   ├── shared-kernel/          ← utilidades comunes de dominio
│   ├── observability/          ← logging, tracing, métricas
│   └── security/               ← filtros JWT, utilidades de autorización
│
├── config-server/
│   ├── pom.xml
│   └── src/main/resources/application.yml
│
├── discovery-server/           ← Eureka Server
│   ├── pom.xml
│   └── src/main/resources/application.yml
│
├── tests/
│   ├── identity-access-service/
│   │   ├── src/test/java/...   ← unit + integration (Testcontainers)
│   │   └── pom.xml
│   ├── administration-service/
│   ├── reporting-service/
│   └── audit-service/
│
├── deploy/
│   ├── docker/
│   └── compose/
│
└── docker-compose.yml
```

---

# 24. Estructura interna de un microservicio

Se sigue **Clean Architecture / Hexagonal** dentro de cada servicio. Los módulos internos son paquetes Maven que dependen en una sola dirección: `api → application → domain ← infrastructure`.

Ejemplo para `course-service`:

```text
administration-service/
│
├── pom.xml
│
└── src/
    ├── main/
    │   ├── java/com/backoffice/administration/
    │   │   ├── AdministrationApplication.java  ← @SpringBootApplication
    │   │   │
    │   │   ├── api/
    │   │   │   ├── controllers/
    │   │   │   │   ├── ParameterController.java
    │   │   │   │   ├── ModelProviderController.java
    │   │   │   │   └── ModelFunctionController.java
    │   │   │   ├── dto/
    │   │   │   ├── exception/                 ← @RestControllerAdvice
    │   │   │   └── config/                    ← seguridad, validación
    │   │   │
    │   │   ├── application/
    │   │   │   ├── commands/
    │   │   │   │   ├── UpdateParameterCommand.java
    │   │   │   │   ├── RegisterModelProviderCommand.java
    │   │   │   │   └── AssignModelToFunctionCommand.java
    │   │   │   ├── queries/
    │   │   │   │   ├── GetParameterQuery.java
    │   │   │   │   └── GetModelProvidersQuery.java
    │   │   │   ├── dto/
    │   │   │   ├── validators/
    │   │   │   └── ports/                     ← interfaces hacia dominio/infra
    │   │   │
    │   │   ├── domain/
    │   │   │   ├── model/                     ← entidades + value objects
    │   │   │   ├── events/                    ← domain events
    │   │   │   ├── exceptions/
    │   │   │   └── services/
    │   │   │
    │   │   └── infrastructure/
    │   │       ├── persistence/               ← JPA repositories, entities
    │   │       │   ├── entity/
    │   │       │   └── repository/
    │   │       ├── messaging/
    │   │       │   ├── outbox/                ← Transactional Outbox
    │   │       │   └── kafka/                 ← producers, consumers, topics, consumer groups
    │   │       └── external/                  ← clientes REST hacia otros servicios
    │   │
    │   └── resources/
    │       ├── application.yml
    │       ├── application-development.yml
    │       └── db/migration/                  ← Flyway migrations
    │
    └── test/java/com/backoffice/administration/
        ├── unit/
        └── integration/
```

---

# 25. Responsabilidad de cada capa

## API

Responsable de:

- HTTP.
- Model binding.
- Autorización.
- Status codes.
- DTOs de entrada/salida.

No debe contener reglas complejas de negocio.

## Application

Responsable de:

- Casos de uso.
- Orquestación.
- Validaciones.
- Interfaces.
- DTOs.

## Domain

Responsable de:

- Reglas de negocio.
- Entidades.
- Value Objects.
- Domain Events.
- Invariantes.

## Infrastructure

Responsable de:

- PostgreSQL.
- JPA/Hibernate (Spring Data JPA).
- Flyway (migraciones).
- Kafka (Spring for Apache Kafka).
- Outbox.
- Repositorios.
- Clientes HTTP hacia otros servicios.
- Servicios externos.

---

# 25bis. Patrones de diseño aplicados

Mapa de patrones GoF/Catálogo con el lugar concreto donde se aplican, el requisito que resuelven y el beneficio. Los nombres de paquete siguen la estructura `com.backoffice.<servicio>`.

## Domain

### Specification — Reglas de negocio compuestas

- **Dónde:** `com.backoffice.identity.domain.specifications` — `AtLeastOneActiveAdminSpec` (RF-ROL-05); `com.backoffice.administration.domain.specifications` — reglas de cambio de proveedor/parámetro (RF-CFG-06, RF-IA-35).
- **Resuelve:** RF-ROL-05, RF-CFG-06, RF-IA-35.
- **Beneficio:** reglas combinables (AND/OR), testeables aisladamente y reutilizables entre servicios. Evita `if` dispersos con condiciones de negocio.

### Strategy — Políticas intercambiables

- **Dónde:** `com.backoffice.administration.domain.retention` — `RetentionDecisionStrategy` (extender vs anonimizar, RF-RET-03).
- **Resuelve:** RF-RET-03.
- **Beneficio:** el caso de uso no cambia cuando se ajusta una política; la variación queda encapsulada.

### Null Object — Dependencia externa no disponible

- **Dónde:** `com.backoffice.administration.infrastructure.external` — `ProviderRegistryClient` + `NoOpProviderClient` (fallback ante caída del AI Service).
- **Resuelve:** resiliencia (RNF-06).
- **Beneficio:** elimina `if (client == null)` y deja un comportamiento neutro explícito, fácil de testear.

## Application

### Command — Casos de uso como objetos

- **Dónde:** `com.backoffice.administration.application.commands` — `UpdateParameterCommand`, `RegisterModelProviderCommand`, `AssignModelToFunctionCommand`; `com.backoffice.identity.application.commands` — `DeleteAdminCommand`.
- **Resuelve:** estructura de capas de §24; CQRS ligero.
- **Beneficio:** aísla el HTTP del dominio; cada comando es testeable y auditable por separado.

### Chain of Responsibility — Pipeline de validación

- **Dónde:** `com.backoffice.identity.application.validation` — `AdminDeletionValidator` (auto-eliminación → 2FA → confirmación escrita → último ADMIN). `com.backoffice.reporting.api.security` — validador de alcance por curso.
- **Resuelve:** RF-ROL-06, RF-RPT-04.
- **Beneficio:** cada validador es independiente, se agrega/quita sin tocar el flujo y falla con su propio error.

### Decorator — Auditoría transversal

- **Dónde:** `com.backoffice.audit.application` — `AuditableCommandDecorator` que envuelve comandos administrativos.
- **Resuelve:** RF-AUD-01/03.
- **Beneficio:** la auditoría se compone sobre el comando sin ensuciar el caso de uso; se aplica selectivamente a las acciones sensibles.

## Infrastructure

### Adapter — Clientes a servicios externos

- **Dónde:** `com.backoffice.*.infrastructure.external` — `IdentityClient`, `AIProviderClient` (para verificar config), `CourseClient` (consulta administrativa cross-team).
- **Resuelve:** contratos cross-team (§35.2).
- **Beneficio:** aísla la red y el mapeo de DTOs; testear con mocks de la interfaz.

### Repository — Persistencia

- **Dónde:** `com.backoffice.*.infrastructure.persistence.repository` (Spring Data JPA).
- **Resuelve:** RNF-04 (Database per Service).
- **Beneficio:** abstrae el acceso a datos del dominio.

### Observer + Outbox — Eventos de dominio

- **Dónde:** `com.backoffice.*.domain.events` + `infrastructure.messaging.outbox`.
- **Resuelve:** §12/§13, ADR-003.
- **Beneficio:** el dominio publica eventos puros; el Outbox garantiza la entrega en la misma transacción.

### Unit of Work — Transacción con Outbox

- **Dónde:** servicio transaccional (`@Transactional`) que persiste entidad + `OutboxMessage` en el mismo commit.
- **Resuelve:** §13.
- **Beneficio:** consistencia entre el cambio de negocio y su evento (nunca un commit sin evento, ni evento sin commit).

### Idempotency Key — Operaciones críticas

- **Dónde:** `*.api.filter` / `*.application.commands` — PUT de configuración, alta/baja de proveedor, baja de ADMIN.
- **Resuelve:** duplicados + 429 (§9.1).
- **Beneficio:** ante un duplicado, el backend responde el resultado original en vez de re-ejecutar.

## Tabla resumen

| Patrón | Paquete propuesto | RF | Servicio |
|---|---|---|---|
| Specification | `identity.domain.specifications` · `administration.domain.specifications` | RF-ROL-05 · RF-CFG-06 | Identity / Administration |
| Strategy | `administration.domain.retention` | RF-RET-03 | Administration |
| Null Object | `administration.infrastructure.external` | RNF-06 | Administration |
| Command | `*.application.commands` | §24 | Todos |
| Chain of Responsibility | `identity.application.validation` | RF-ROL-06 · RF-RPT-04 | Identity / Reporting |
| Decorator | `audit.application` | RF-AUD-01/03 | Audit |
| Adapter | `*.infrastructure.external` | §35.2 | Todos |
| Repository | `*.infrastructure.persistence` | RNF-04 | Todos |
| Observer + Outbox | `*.domain.events` · `*.messaging.outbox` | §12/§13 | Todos |
| Unit of Work | servicio `@Transactional` | §13 | Todos |
| Idempotency Key | `*.api.filter` | §9.1 | Todos (críticas) |

---

# 26. Configuración

## 26.1 Variables de entorno

Ejemplo:

```text
SPRING_PROFILES_ACTIVE=development

JWT_SECRET=...
JWT_ISSUER=backoffice
JWT_AUDIENCE=backoffice-api

DATABASE_URL=jdbc:postgresql://postgres:5432/administration_db
DATABASE_USERNAME=...
DATABASE_PASSWORD=...

KAFKA_BOOTSTRAP_SERVERS=kafka:9092
KAFKA_GROUP_ID=administration-service

EUREKA_CLIENT_URL=http://eureka:8761/eureka
CONFIG_SERVER_URL=http://config-server:8888

LLM_API_KEY=...            # si aplica a integraciones de IA
BREAK_GLASS_SECRET=...     # secreto del mecanismo de recuperación de ADMIN
```

Los valores reales sensibles no se subirán al repositorio.

---

## 26.2 application.yml

Ejemplo:

```yaml
server:
  port: 8080

spring:
  application:
    name: administration-service
  datasource:
    url: ${DATABASE_URL}
    username: ${DATABASE_USERNAME}
    password: ${DATABASE_PASSWORD}
  jpa:
    hibernate:
      ddl-auto: validate
    open-in-view: false
  flyway:
    enabled: true

app:
  jwt:
    issuer: ${JWT_ISSUER}
    audience: ${JWT_AUDIENCE}

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
```

Los secretos deberán sobrescribirse mediante variables de entorno o Secret Manager.

---

# 27. Docker Compose

Infraestructura local:

```text
┌───────────────────────────────────────────┐
│               Docker Compose              │
│                                           │
│  discovery-server (Eureka)                │
│  config-server                           │
│  gateway                                 │
│  identity-access-service                 │
│  administration-service                  │
│  reporting-service                       │
│  audit-service                           │
│                                           │
│  PostgreSQL (una base por servicio)       │
│  Kafka (management)            │
└───────────────────────────────────────────┘
```

Ejemplo conceptual:

```yaml
services:

  discovery-server:
    build: ./discovery-server
    ports:
      - "8761:8761"

  config-server:
    build: ./config-server
    ports:
      - "8888:8888"
    depends_on:
      - discovery-server

  gateway:
    build: ./gateway
    ports:
      - "8080:8080"
    depends_on:
      - discovery-server
      - config-server

  identity-access-service:
    build: ./services/identity-access-service

  administration-service:
    build: ./services/administration-service

  reporting-service:
    build: ./services/reporting-service

  audit-service:
    build: ./services/audit-service

  reporting-service:
    build: ./services/reporting-service

  kafka:
    image: apache/kafka:3.9.0      # KRaft (sin ZooKeeper)
    ports:
      - "9092:9092"
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1

  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: backoffice
      POSTGRES_PASSWORD: ${DATABASE_PASSWORD}
```

El archivo real deberá adaptarse a la configuración definitiva de las bases de datos y credenciales. Cada microservicio usa una base PostgreSQL propia con separación lógica de ownership (Database per Service). Kafka se ejecuta en modo single-node con KRaft para simplificar el entorno local; los topics se crean automáticamente con `auto.create.topics.enable` o mediante una estrategia de provisioning en el arranque de cada servicio.

---

# 28. Contratos de API

Los endpoints deberán seguir una convención REST consistente.

> **Auth y gestión de cuentas ADMIN pertenecen al Tema 01** (`/api/auth/*`, `/api/admin/accounts/*`): el Backoffice los **consume** a través del gateway de plataforma (no los implementa). Los endpoints propios del Backoffice son los de **Administration** y **Reporting** de abajo.

## Administration & Configuration

```http
GET  /api/administration/parameters
GET  /api/administration/parameters/{key}
PUT  /api/administration/parameters/{key}

GET  /api/administration/model-providers
POST /api/administration/model-providers
PUT  /api/administration/model-providers/{id}
DELETE /api/administration/model-providers/{id}

GET  /api/administration/model-functions
PUT  /api/administration/model-functions/{function}

POST /api/administration/evaluator/activate
GET  /api/administration/evaluator/calibration
```

## Reporting & Analytics (reportes, métricas, export, alertas)

```http
GET    /api/reports/platform
GET    /api/reports/courses/{courseId}
GET    /api/reports/courses/{courseId}/metrics
GET    /api/reports/courses/{courseId}/teacher        ← panel del profesor (alumno en riesgo)
GET    /api/reports/courses/{courseId}/teacher/risk
GET    /api/export/courses/{courseId}                ← exportación (CSV/PDF)
GET    /api/export/platform
GET    /api/alerts                                   ← alertas configurables (para más adelante)
```

## Auditoría — consumida del Tema 01

```http
GET /api/audit        ← contrato de lectura con el Tema 01
GET /api/audit/{id}
```

Los endpoints son una propuesta de diseño y deberán ajustarse a los casos de uso definitivos.

## 28.1 Matriz endpoint → rol → alcance

| Endpoint | Método | Roles | Regla de alcance |
|---|---|---|---|
| `/api/auth/*`, `/api/admin/accounts*` | — | Tema 01 | consumidos, no implementados |
| `/api/administration/parameters` | GET | ADMIN, PROFESOR | PROFESOR: solo lectura |
| `/api/administration/parameters/{key}` | PUT | ADMIN | exclusivo ADMIN (RF-CFG-05) |
| `/api/administration/model-providers*` | GET/POST/PUT/DELETE | ADMIN | exclusivo ADMIN (RF-IA-35) |
| `/api/administration/model-functions*` | GET/PUT | ADMIN | exclusivo ADMIN (RF-IA-23/24) |
| `/api/administration/evaluator/*` | GET/POST | ADMIN | exclusivo ADMIN (RF-IA-25/28/31) |
| `/api/audit` | GET | ADMIN | global (lectura Tema 01) |
| `/api/reports/platform` | GET | ADMIN | global |
| `/api/reports/courses/{courseId}*` | GET | ADMIN, PROFESOR | PROFESOR: solo su cohorte (matrícula Tema 02) |
| `/api/reports/courses/{courseId}/teacher*` | GET | PROFESOR, ADMIN | PROFESOR: solo su cohorte; **sin comparación entre docentes** |
| `/api/export/*` | GET | ADMIN (platform) / PROFESOR (su cohorte) | según recurso |
| `/api/alerts` | GET | ADMIN, PROFESOR | según recurso |

> La autorización se valida **siempre** en el microservicio propietario (RNF-03), incluso cuando el Gateway ya validó el JWT. La pertenencia del PROFESOR a una cohorte se valida contra la **matrícula del Tema 02** (cross-team, §17).

---

# 29. Manejo de errores

Todos los servicios utilizarán un formato uniforme de error.

Ejemplo:

```json
{
  "type": "https://example.com/errors/business-rule",
  "title": "Business rule violation",
  "status": 409,
  "detail": "Cannot delete the last active ADMIN.",
  "traceId": "abc-123"
}
```

Códigos esperados:

| Código | Uso |
|---:|---|
| 200 | Operación exitosa |
| 201 | Recurso creado |
| 204 | Operación exitosa sin contenido |
| 400 | Datos inválidos |
| 401 | No autenticado |
| 403 | Sin permisos |
| 404 | Recurso inexistente |
| 409 | Conflicto/regla de negocio |
| 422 | Validación semántica |
| **429** | **Demasiadas solicitudes (rate limiting)** — con header `Retry-After` |
| 500 | Error inesperado |
| 503 | Dependencia no disponible |

## 29.1 Catálogo de códigos de error de negocio

| Código de error | HTTP | Descripción |
|---|---|---|
| `RATE_LIMIT_EXCEEDED` | 429 | Se superó el límite de peticiones (rate limiting en Gateway, §9.1); incluye `Retry-After` |
| `DUPLICATE_REQUEST` | 409 | Se reenvió una `Idempotency-Key` ya procesada; se devuelve el resultado original |
| `LAST_ADMIN_PROTECTION` | 409 | La operación dejaría la plataforma sin ADMIN activo (RF-ROL-05) |
| `ADMIN_SELF_DELETION` | 409 | Un ADMIN no puede eliminarse a sí mismo (RF-ROL-02) |
| `ADMIN_DELETION_2FA_REQUIRED` | 403 | Baja de ADMIN exige contraseña + 2FA (RF-ROL-06) |
| `PARAMETER_FORBIDDEN_FOR_ROLE` | 403 | PROFESOR intentó modificar un parámetro global (RF-CFG-05) |
| `PARAMETER_HISTORY_IMMUTABLE` | 409 | No se recalculan datos históricos (RF-CFG-06) |
| `MODEL_PROVIDER_FORBIDDEN_FOR_ROLE` | 403 | Solo ADMIN gestiona proveedores de modelo (RF-IA-35) |
| `MODEL_EVALUATOR_CALIBRATION_REQUIRED` | 409 | Cambio de evaluador exige calibración aprobada (RF-IA-31) |
| `COURSE_NOT_ACCESSIBLE` | 403 | El usuario no tiene alcance sobre el curso (§17) — validado cross-team |
| `REPORT_ANONYMITY_VIOLATION` | 409 | Operación intenta reconstruir autor ↔ respuesta de encuesta (RF-ENC-04) |
| `RESOURCE_NOT_FOUND` | 404 | Recurso inexistente |
| `VALIDATION_FAILED` | 422 | Error de validación semántica |
| `EXTERNAL_DEPENDENCY_UNAVAILABLE` | 503 | Servicio externo no disponible |

---

# 30. Concurrencia y reglas críticas

Algunas reglas no pueden depender únicamente de validaciones previas.

Por ejemplo, la protección del último ADMIN debe considerar concurrencia.

Escenario:

```text
ADMIN A elimina ADMIN B
ADMIN C elimina ADMIN D
```

Ambas operaciones podrían verificar simultáneamente que existen varios ADMIN.

Por ello, la operación deberá utilizar:

- Transacción.
- Bloqueo apropiado o estrategia de concurrencia.
- Revalidación antes de confirmar.

La regla debe garantizar que nunca se llegue a cero ADMIN activos.

---

# 31. Auditoría distribuida

La auditoría se realizará mediante eventos: el Backoffice **emite** los eventos de auditoría de sus acciones administrativas; el **Tema 01** los persiste y permite su consulta.

```text
Administration & Configuration Service
      │
      │ GlobalConfigurationChanged / ModelProviderChanged
      ▼
   Kafka · topic administration.events
      │
      ▼
 Tema 01 — Identidad (persiste auditoría)
      │
      ▼
   (base del Tema 01)
```

Cada evento debe conservar:

```text
eventId
correlationId
actorId
actorRole
timestamp
action
resource
resourceId
result
reason
```

Esto permite responder:

> ¿Quién realizó esta acción, sobre qué recurso, cuándo y como consecuencia de qué request?

---

# 32. Observabilidad

> **Alcance MVP:** health checks + logs estructurados (obligatorio). Dashboards (Prometheus/Grafana/OpenTelemetry) quedan **opcionales según tiempo disponible**; la instrumentación se deja preparada (métricas expuestas vía `/actuator/metrics`).

Cada servicio expondrá:

```text
GET /health
GET /health/ready
GET /health/live
```

Los logs deberán ser estructurados.

Ejemplo:

```json
{
  "timestamp": "2026-08-28T12:00:00Z",
  "level": "Information",
  "service": "administration-service",
  "correlationId": "abc-123",
  "userId": "user-01",
  "action": "ModelProviderChanged",
  "resourceId": "provider-42"
}
```

---

# 33. Pruebas

## 33.1 Unitarias

Framework: **JUnit 5 + Mockito**.

Se probarán:

- Reglas de dominio.
- Validadores.
- Casos de uso.
- Reglas de autorización críticas.

Especial atención a:

- Último ADMIN.
- Auto-eliminación.
- Configuración global y proveedores de modelo.
- Reportes/métricas/exportación (alcance y anonimato de encuestas).
- Retención.

## 33.2 Integración

Framework: **Spring Boot Test + Testcontainers** (bases PostgreSQL reales en contenedores).

Se probará:

- API + DB (MockMvc / WebTestClient).
- JPA / Hibernate.
- Flyway (migraciones).
- Kafka (Testcontainers Kafka: producción y consumo de eventos).
- Outbox.
- Consumo de eventos (idempotencia, re-procesamiento).
- Autenticación.
- Autorización.

## 33.3 Contract testing

Los contratos de eventos deberán verificarse para evitar que un cambio de productor rompa consumidores. Se propone **Spring Cloud Contract** o **Pact**.

> **Opcional según tiempo:** para el MVP, el contrato queda cubierto por OpenAPI (HTTP) + esquemas de eventos versionados (§12) + tests de integración. Spring Cloud Contract/Pact se incorpora si el tiempo lo permite.

## 33.4 Pruebas de carga

El escenario de carga deberá contemplar el objetivo de **120 sesiones concurrentes** definido por el PRD.

---

# 34. Trazabilidad RF → componente

| Requerimiento | Servicio / Tema |
|---|---|
| RF-CFG-* | Administration & Configuration (Backoffice) |
| RF-IA-ADM-* (proveedores de modelo) | Administration & Configuration (Backoffice) |
| RF-RPT-* / RF-REP-* (reportes, métricas, export, alertas) | Reporting & Analytics (Backoffice) |
| RF-ROL-* · RF-USR onboarding · RF-AUD-* · RF-RET-* | **Tema 01** (consumidos) |
| RF-CUR-* (cohorte) · matrícula | **Tema 02** (consumidos) |
| RF-DES-*, RF-IA-* producto, gamificación, ranking, chat, encuestas | Temas 03-11 (solo lectura 02/04/05/07/08/10) |
| RNF seguridad | Gateway (Tema 01) + servicios |
| RNF observabilidad / escalabilidad / idempotencia | Todos |

> **Backoffice = consumidor puro:** no implementa identidad, cohorte, desafíos, economía ni auditoría (Tema 01 los consume; Temas 03-11 solo lectura).

---

# 35. Qué pertenece a cada equipo

Según `TUP_PIV_BE_PROPUESTA_ARQ.pdf`, el Backoffice es el **Tema 12** con **2 servicios propietarios**:

```text
BACKOFFICE (Tema 12) — 2 servicios propietarios
  ├── Administration & Configuration   (PAR + proveedor LLM, exclusiva ADMIN)
  └── Reporting & Analytics            (reportes, panel, métricas, export, alertas)

Consume: Tema 01 (identidad/auth/roles/auditoría) · Tema 02 (cohorte/matrícula)
Lectura: 02, 04, 05, 07, 08, 10 (contratos de lectura)
```

El equipo de BackOffice no debe implementar el frontend administrativo salvo que la división del curso lo determine expresamente. Si lo determina, el plan está definido en la materia Front: app Angular SSR + BFF BackOffice + Nginx (ver página `frontend/arquitectura-despliegue` y `sdd/frontend`).

## 35.1 Matriz de ownership por tema

| Tema | Dominio | Rol del Backoffice |
|---|---|---|
| **T12 (Backoffice)** | Administración de plataforma, **PAR-01..24**, proveedor LLM (exclusiva ADMIN), reportes, panel, métricas, export, alertas | **Propietario (2 servicios)** |
| **T01** | Identidad, auth, 2FA, roles, token, sesión, **auditoría**, retención, **API Gateway** | **Consume** (auth/autorización/auditoría; gateway de plataforma) |
| **T02** | Cursos y Matrícula (curso-cohorte, padrón, invitación) | **Consume** (cohorte `course_id`, pertenencia docente) |
| T03 | Motor de Desafíos | Solo lectura |
| T04 | Teóricos y Encuestas | Solo lectura (agregados anónimos) |
| T05 | Desafíos Prácticos | Solo lectura |
| T06 | Sandbox/Runtime | — |
| T07 | Evaluación LLM | Solo lectura (consume config de proveedores que administramos) |
| T08 | Banco | Solo lectura |
| T09 | Mercado | — |
| T10 | Roadmap y Progreso | Solo lectura |
| T11 | Social y Notificaciones | — |

> Matriz a acordar con los demás equipos en la sesión de integración (regla "cada entidad tiene un único dueño").

## 35.2 Contratos cross-team (dependencias críticas)

### 1. Configuración de proveedores de modelo → T07 (Evaluación LLM)
- **Mecanismo:** evento `ModelProviderChanged` / `GlobalConfigurationChanged` en `administration.events`; el T07 la consume.
- **Regla:** solo ADMIN puede cambiarla (RF-IA-35).

### 2. Parámetros de economía → los aplican T03, T05, T08, T10
- **Mecanismo:** evento `GlobalConfigurationChanged` (`{key, value, version}`) en `administration.events`; esos temas leen la configuración (no la tienen hardcodeada).
- **Regla:** cambios hacia adelante (RF-CFG-06).

### 3. Contratos de lectura del Backoffice (consumidor puro)
- **Mecanismo:** Reporting & Analytics (Backoffice) lee de los temas **02, 04, 05, 07, 08, 10** (eventos/APIs a través del gateway) para construir read models. **Sin esos contratos en el sprint 1 no hay nada demostrable.**
- **Encuestas:** solo agregados anónimos (RF-ENC-04/12).

### 4. Autorización y auditoría
- **Auth/autorización:** el gateway (T01) valida el token y propaga contexto; la decisión de autorización la toma el servicio dueño de la regla (validar ≠ autorizar).
- **Auditoría:** el Backoffice emite eventos de auditoría de sus acciones; T01 los persiste.

> **Convención de eventos:** todos los eventos siguen `{eventId, eventType, occurredAt, correlationId, actorId, source, payload}` (ver §12), con contrato versionado (§11/33.3).

---

# 36. Decisiones arquitectónicas

## ADR-001 — Microservicios

**Decisión:** utilizar microservicios.

**Motivos:**

- Requisito explícito de los docentes.
- Permite experimentar con comunicación distribuida.
- Separación de dominios.
- Independencia de despliegue.
- Facilita comprender resiliencia, eventos y consistencia distribuida.

**Consecuencia:** mayor complejidad operacional.

---

## ADR-002 — Database per Service

**Decisión:** cada microservicio posee su propia base.

**Motivo:** evitar acoplamiento mediante base compartida.

**Consecuencia:** no existen transacciones distribuidas simples y se requiere comunicación mediante APIs/eventos.

---

## ADR-003 — Broker de eventos: Kafka

**Decisión:** utilizar **Apache Kafka** como broker de eventos de dominio, siguiendo el patrón **híbrido**: REST por el gateway para lo síncrono + eventos por Kafka para avisar cambios de configuración, con **caché local con TTL** en los consumidores.

**Estado:** decisión del equipo, **coordinada con la plataforma** (los grupos de Notificaciones y Banco también usan Kafka → coherencia e integración) y **a validar con la cátedra** (el broker es infraestructura compartida).

**Motivos:**

- **Alineación de plataforma:** otros dominios (Notificaciones, Banco) ya adoptaron Kafka → un único broker para toda la plataforma simplifica integración, contratos y operación.
- **Replay / histórico:** Kafka conserva los eventos por retención configurable y permite re-leer un topic desde un offset anterior. Es una **capacidad disponible** para reconstruir read models o auditar, sin depender de un mecanismo externo.
- **Orden garantizado por partición:** particionando por clave de negocio (`courseId`/`key`) se garantiza orden por curso sin bloqueos globales.
- **Escalabilidad y robustez:** particiones + consumer groups permiten escalar el procesamiento por dominio y tolerar caídas del consumidor (offset persistente).
- **Soporte maduro con Spring:** Spring for Apache Kafka (KafkaTemplate/@KafkaListener) con buena integración en Spring Boot.

**Alternativa considerada — RabbitMQ:**

- Es más liviano y excelente para *work-queues* punto a punto, pero **no es la decisión de la plataforma**: los demás grupos ya eligieron Kafka.
- Los read models de Reporting se reconstruyen **vía contratos de lectura REST** desde los dominios dueños (no dependemos del replay del broker, aunque Kafka lo ofrece).
- Se mantiene **documentado como alternativa** (patrón híbrido idéntico: REST + eventos + caché TTL + Outbox).

**Decisión técnica de consumo:**

- **Outbox** para publicar eventos en la misma transacción (sin pérdida).
- **At-least-once** + **idempotencia** por `event_id` **y por `version`** en los consumidores (descarta `v ≤ local`).
- Topic por dominio y **particiones por clave de negocio**; cada servicio es un **consumer group** con su offset; **caché local con TTL 10 min** + invalidación por evento en los consumidores de parámetros (respaldo ante caída del Backoffice).

---

## ADR-004 — Outbox

**Decisión:** utilizar Transactional Outbox.

**Motivo:** evitar pérdida de eventos después de confirmar una transacción.

---

## ADR-005 — API Gateway

**Decisión:** utilizar Gateway como entrada única del BackOffice.

**Motivo:**

- Routing.
- Seguridad inicial.
- Rate limiting.
- Correlation ID.
- Punto de entrada uniforme.

---

## ADR-006 — Autorización distribuida

**Decisión:** validar autorización tanto en Gateway como en microservicio.

**Motivo:** el Gateway no debe ser la única frontera de seguridad.

---

## ADR-007 — Stack Java + Spring

**Decisión:** Java 21 + Spring Boot 3 + Maven para todos los microservicios del BackOffice.

**Motivo:** experiencia previa del equipo en el cuatrimestre anterior con Java y Spring; ecosistema maduro de Spring Cloud (Gateway, Eureka, Config Server, Stream/for Kafka) que cubre todas las necesidades de la arquitectura.

**Consecuencia:** se reemplaza cualquier propuesta previa basada en .NET (YARP, EF Core) por Spring Cloud Gateway, JPA/Hibernate y PostgreSQL.

---

## ADR-008 — Service Discovery con Eureka

**Decisión:** Spring Cloud Netflix **Eureka** como Service Discovery.

**Motivo:** estándar del ecosistema Spring Cloud, integración nativa y balanceo del lado del cliente.

**Alternativa documentada:** Consul (agrega KV store para config), disponible si el equipo prefiere consolidar discovery + config en un componente.

---

## ADR-009 — Base de datos PostgreSQL (recomendada)

**Decisión:** PostgreSQL como motor de base (una por microservicio).

**Motivo:** open source, mejor combinación con Hibernate, soporte JSONB para auditoría/eventos, imagen Docker liviana y transacciones sólidas. **Pendiente de confirmación final del equipo** (SQL Server también es viable con Spring).

---

# 37. Riesgos técnicos

| Riesgo | Impacto | Mitigación |
|---|---|---|
| Complejidad de microservicios | Alto | Mantener pocos servicios y dominios claros |
| Saturación/abuso de endpoints administrativos (429) | Medio | Rate limiting en Gateway (Bucket4j/Redis) + Idempotency Keys (§9.1) |
| Fallos de Kafka (broker caído) | Medio | Outbox + retry + broker resiliente |
| Eventos duplicados | Medio | Idempotencia |
| Inconsistencia eventual | Medio | Diseñar claramente qué operaciones requieren respuesta síncrona |
| Pérdida de trazabilidad | Alto | Correlation ID |
| Borrado accidental | Alto | Baja lógica + reglas de negocio |
| Eliminación del último ADMIN | Crítico | Transacción + regla incondicional |
| Acceso indebido entre cursos | Alto | Autorización por recurso |
| Exposición de secretos | Alto | Variables de entorno/secrets |
| Acoplamiento entre servicios | Alto | APIs/eventos y ausencia de DB compartida |

---

# 38. Qué NO se debe hacer

## No compartir una base entre microservicios

Incorrecto:

```text
Identity ──┐
Admin ─────┼──► PostgreSQL compartido (misma tabla de negocio)
Reporting ─┘
```

Correcto:

```text
Administration ──► administration_db
Reporting ──────► reporting_db
(identidad y auditoría viven en el Tema 01, no acá)
```

---

## No acceder a tablas de otro servicio

Incorrecto:

```java
courseRepository.findByUserEmail(...);   // consulta directa a datos de otro dominio
```

Correcto:

```text
Administration Service
      │
      ▼
Identity & Access API
```

o mediante un evento/read model cuando no se requiera respuesta inmediata.

---

## No colocar reglas de negocio en Controllers

Incorrecto:

```text
Controller
  ├── valida último ADMIN
  ├── calcula permisos
  ├── modifica DB
  └── publica evento
```

Correcto:

```text
Controller
   │
   ▼
Application
   │
   ▼
Domain
   │
   ▼
Infrastructure
```

---

## No confiar únicamente en el frontend

Una restricción administrativa siempre debe validarse en backend.

Ejemplo:

```text
Frontend: "oculto botón eliminar"
             ≠
Backend: "operación imposible"
```

---

# 39. Definition of Done del BackOffice

Una funcionalidad se considerará terminada cuando:

- [ ] Tiene RF asociado.
- [ ] Tiene caso de uso definido.
- [ ] Tiene endpoint documentado.
- [ ] Tiene autorización implementada.
- [ ] Tiene validaciones de dominio.
- [ ] Tiene persistencia correspondiente.
- [ ] Tiene auditoría si corresponde.
- [ ] Tiene eventos si corresponde.
- [ ] Tiene pruebas unitarias.
- [ ] Tiene pruebas de integración cuando corresponde.
- [ ] Tiene manejo de errores.
- [ ] Tiene logs.
- [ ] Propaga Correlation ID.
- [ ] No accede directamente a la DB de otro microservicio.
- [ ] Está documentada en Swagger.
- [ ] Puede ejecutarse mediante Docker Compose.
- [ ] Tiene migraciones de base de datos.
- [ ] Está contemplada en la matriz de trazabilidad.

---

# 40. Alcance recomendado para el MVP del equipo

### Prioridad 1 — Obligatorio (pedido para empezar)

- **Administración de plataforma** (operativa sobre config y proveedores).
- **Configuración global** (PAR-01..24; base PRD PAR-01..18, registro extensible).
- **Gestión de proveedores de modelo** (alta/baja, asignación modelo↔función, evaluador activo) — exclusiva ADMIN.
- **Reportes docentes**.
- **Contratos de lectura** con los 6 temas (02/04/05/07/08/10) — sin ellos no hay nada demostrable (consumidor puro).
- Autenticación/autorización: **consumida** del Tema 01 (no se implementa).
- Seguridad (rate limiting, secretos), Docker, Swagger, pruebas.

### Prioridad 2 (para más adelante)

- Panel del profesor con **indicador de alumno en riesgo**.
- **Exportación de datos**.
- **KPIs con CSAT 5★** y **alertas configurables**.
- **Frescura ≤ 15 min** en read models y **sin comparación entre docentes**.

### Prioridad 3

- Optimizaciones.
- Escalabilidad horizontal.
- Métricas avanzadas.
- Automatización adicional.

La prioridad técnica no modifica el alcance funcional establecido por el PRD; sirve únicamente para organizar la implementación del equipo.

---

# 40bis. Planificación y dimensionamiento

Tareas asignadas (Tema 12) según `TUP_PIV_BE_PROPUESTA_ARQ.pdf`, en las 3 columnas del documento (MoSCoW) y dimensionadas en **talles T-shirt (S/M/L)**.

## 🟢 Must — Pedido para empezar (sprint 1)

| Ítem | RF | Subtareas | Talla | Dependencia |
|---|---|---|---|---|
| Administración de plataforma | RF-CFG-01/05 | Operativa de ADMIN sobre config/proveedores · consumo de auth/roles (T01) · permisos de endpoints | M | T01 |
| Registro de parámetros PAR-01..24 | RF-CFG-04/06 | CRUD `GlobalParameter` · versionado · hacia adelante · evento `GlobalConfigurationChanged` | M | Temas 03/05/08/10 |
| Gestión del proveedor LLM (exclusiva ADMIN) | RF-IA-ADM-01..07 | CRUD proveedores · modelo↔función · evaluador único · golden set + calibración · deriva | L | T07 consume |
| Contratos de lectura con los 6 temas | RF-RPT-10 | Acordar contratos (02/04/05/07/08/10) · suscripción a eventos · adapters · read models | L | Temas 02/04/05/07/08/10 |
| Reportes docentes | RF-RPT-01 | Read models por cohorte · endpoints de reporte · autorización por matrícula (T02) | M | Contratos |

## 🟡 Should — Para más adelante

| Ítem | RF | Subtareas | Talla | Dependencia |
|---|---|---|---|---|
| Panel del profesor (alumno en riesgo) | RF-RPT-03 | `AtRiskStudent` · indicador · endpoint | M | Lecturas T04/05/08/10 |
| Frescura ≤ 15 min | RF-RPT-06 | SLA de frescura · monitoreo de lag | S-M | Contratos |
| KPIs CSAT 5★ | RF-RPT-02 | Agregados anónimos · KPI por cohorte | S-M | T04 |
| Alertas configurables | RF-RPT-05 | Reglas configurables · `/api/alerts` | S-M | Lecturas |
| Sin comparación entre docentes | RF-RPT-07 | Scope no cross-docente · tests | S | — |

## 🔵 Could — Podría ser

| Ítem | RF | Subtareas | Talla | Dependencia |
|---|---|---|---|---|
| Exportación de datos | RF-RPT-04 | CSV/PDF · `/api/export/*` | M | Read models |

> Criterio del documento: "Pedido para empezar" = núcleo + lo que otros equipos necesitan (los **contratos de lectura** son la dependencia crítica del sprint 1). "Para más adelante" = se diseña ahora y se implementa después. "Podría ser" = extra a medias vale menos que un núcleo terminado.

---

# 40ter. Propuestas de tareas (Must)

Cada tarea Must tiene una **propuesta profesional completa** (objetivo, alcance, RF, diseño técnico con best practices, diagrama, contrato API, modelo de datos, reglas, plan con persona-días, pruebas, DoD y riesgos). Se documenta en el sitio (carpeta `msii/tareas/`) y en `sdd/backend/tareas/`.

| # | Tarea | RF | Talla | Persona-días |
|---|---|---|---|---|
| 1 | Administración de plataforma | RF-CFG-01/05 | M | ~4 |
| 2 | Registro de parámetros PAR-01..24 | RF-CFG-04/06 | M | ~4 |
| 3 | Gestión del proveedor LLM (exclusiva ADMIN) | RF-IA-ADM-01..07 | L | ~8 |
| 4 | Contratos de lectura con los 6 temas | RF-RPT-10 | L | ~7 |
| 5 | Reportes docentes | RF-RPT-01 | M | ~5 |

**Total estimado del núcleo: ~28 persona-días.** Las tareas 3 y 4 son las más grandes (L) y las más riesgosas por dependencias (T07 y los 6 temas). La tarea 4 es la dependencia crítica del sprint 1.

> Best practices aplicadas en todas: Clean Architecture, CQRS (command/query), DTOs + validación, autorización por ámbito (*validar ≠ autorizar*, consume T01), eventos con Outbox + idempotencia, Idempotency-Key, rate limiting, observabilidad (correlation ID, logs, health), OpenAPI, Flyway, soft delete y pruebas (unit + integración con Testcontainers + contract).

---

# 41. Resumen de la arquitectura

La solución propuesta queda resumida de la siguiente manera:

```text
                        BACKOFFICE (Tema 12)
                            │
                            ▼
                 ┌────────────────────────────┐
                 │ API Gateway de PLATAFORMA  │  ← Tema 01 (única puerta)
                 │  T01 · sync por acá        │
                 └────────────┬───────────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
              ▼                               ▼
     ┌─────────────────┐           ┌─────────────────┐
     │ Administration  │           │  Reporting &    │
     │ & Configuration │           │   Analytics     │
     └───────┬─────────┘           └────────┬────────┘
             │                             │
             ▼                             ▼
     administration_db            reporting_db

  Consume: T01 (identidad/auth/roles/auditoría) · T02 (cohorte)
  Lectura: 02/04/05/07/08/10 (contratos de lectura)

                EVENTOS
                   │
                   ▼
              ┌─────────┐
              │  Kafka  │
              └─────────┘

             DISCOVERY / CONFIG
                   │
                   ▼
              ┌─────────┐
              │  Eureka │
              └─────────┘

                   │
                   ▼
              ┌─────────┐
              │ Config  │
              │ Server  │
              └─────────┘
```

---

# 42. Conclusión

El BackOffice (Tema 12) se implementará como **consumidor puro** con **2 servicios propietarios** — **Administration & Configuration** (parámetros PAR + proveedor LLM exclusiva de ADMIN) y **Reporting & Analytics** (reportes docentes, panel del profesor, métricas CSAT, exportación, alertas) — alineado al documento oficial `TUP_PIV_BE_PROPUESTA_ARQ.pdf`.

La arquitectura prioriza:

- Separación de responsabilidades.
- Seguridad (autorización por ámbito; *validar ≠ autorizar*).
- Database per Service.
- **Toda llamada síncrona entre servicios pasa por el gateway** (regla no negociable).
- Eventos mediante Kafka para desacoplamiento (ver ADR-003).
- Transactional Outbox.
- Idempotencia.
- Observabilidad.
- Trazabilidad de requerimientos.
- Evolución independiente de los dominios.

La solución no pretende implementar toda la plataforma educativa dentro del BackOffice. Su objetivo es administrar la plataforma (configuración y proveedores de modelo) y **reportar** consumiendo lecturas de los temas que pertenecen a otros equipos (T01 identidad, T02 cohorte, y lecturas de T02/04/05/07/08/10), sin ser dueño de esos dominios.

---

# Anexo A — Referencias funcionales utilizadas

Este documento se basa en el PRD proporcionado para el proyecto, particularmente en:

- Roles y permisos.
- Configuración.
- Gestión de ADMIN y lado administrativo de usuarios.
- Gestión de proveedores de modelo de IA (RF-IA-23/24/25/35).
- Reportes, métricas y exportación.
- Requerimientos no funcionales.
- Retención.
- Auditoría.
- Definition of Done.
- Riesgos.

Los requerimientos del PRD mantienen sus identificadores originales para facilitar la trazabilidad.

---

# Anexo B — Reglas que requieren especial atención en la defensa

Si los docentes preguntan por qué se tomaron determinadas decisiones:

### ¿Por qué 2 servicios propietarios y no más?

Porque según el documento oficial (`TUP_PIV_BE_PROPUESTA_ARQ.pdf`) el Backoffice es **consumidor puro**: solo es dueño de la administración (PAR + proveedor LLM) y del reporting (reportes, panel, métricas, export, alertas). La identidad, roles, 2FA, auditoría y retención son del **Tema 01**; la cohorte del **Tema 02**; los demás dominios son de los Temas 03-11. Implementar más servicios habría duplicado trabajo de otros equipos.

### ¿Por qué microservicios?

Porque es un requisito del proyecto y además permite separar dominios y experimentar con comunicación distribuida.

### ¿Por qué no una sola base?

Porque se eligió Database per Service para evitar acoplamiento directo entre servicios.

### ¿Por qué Kafka?

Para desacoplar operaciones que no necesitan una respuesta inmediata y propagar eventos de dominio. Se eligió Kafka (sobre RabbitMQ) porque es la **decisión de plataforma** (Notificaciones y Banco lo usan) y aporta **replay/histórico** (reconstruir read models o auditar) y **orden por partición**. Costo reconocido: es más pesado de operar; se mantiene Outbox + idempotencia para la entrega confiable.

### ¿Por qué Outbox?

Para garantizar que una modificación de datos y su evento asociado no queden desincronizados.

### ¿Por qué validar permisos dentro de cada microservicio si ya existe Gateway?

Porque el Gateway es un punto de entrada, no una garantía suficiente de autorización. El servicio propietario debe proteger sus propios recursos.

### ¿Por qué no poner un microservicio por entidad?

Porque eso generaría una fragmentación artificial. La división se realiza por responsabilidades de negocio.

### ¿Por qué no existe un Course Service en el BackOffice?

Porque el dominio de cursos (roadmap, padrón, estados) pertenece al **equipo Cursos** y el de desafíos al **equipo Desafíos**. El BackOffice no los implementa: los **consume** vía eventos (CourseArchived, RosterUpdated) para reportes y métricas. Mantener un servicio propio habría duplicado el trabajo de otro equipo.

### ¿Por qué Audit es independiente?

Porque la auditoría es una responsabilidad transversal y debe conservar evidencia de operaciones sin quedar acoplada a la lógica de cada servicio.

### ¿Por qué Reporting es independiente?

Porque las consultas y agregaciones administrativas no deberían sobrecargar las bases operacionales ni convertirlas en una base de reporting compartida.

