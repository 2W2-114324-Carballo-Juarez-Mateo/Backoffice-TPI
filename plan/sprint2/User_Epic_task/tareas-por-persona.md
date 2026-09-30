# Tareas del Sprint 2 por integrante

> Las líneas de código de cada integrante están en `distribucion-pareja.md` y en su `dev-XX.md`. Acá se ve **qué tarjeta de Taiga le toca a cada uno**, a qué historia pertenece y en qué checkpoint vence.

## Resumen

| Integrante | Backend | Frontend | Test | Documentación | Revisión | Total |
|---|---:|---:|---:|---:|---:|---:|
| Luciano Paz | 2 | 4 | 4 | 3 | 2 | 15 |
| Mateo Carballo | 5 | 4 | 3 | 2 | 1 | 15 |
| Damián Baigorria | 7 | 2 | 0 | 2 | 3 | 14 |
| Joaquín Cortez | 7 | 1 | 0 | 3 | 3 | 14 |
| Valentina Maldonado | 3 | 4 | 1 | 2 | 2 | 12 |
| Máximo Cerquatti | 4 | 1 | 6 | 2 | 1 | 14 |
| Regina Cerasulo | 4 | 1 | 4 | 1 | 2 | 12 |
| Bruno Gianoli | 5 | 0 | 2 | 2 | 1 | 10 |
| Ana Ducart | 0 | 0 | 0 | 11 | 0 | 11 |

> La cantidad de tarjetas **no** es la medida de equilibrio: el reparto es parejo en líneas de código efectivas, no en cantidad de tareas.

## Luciano Paz

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HT03 | Nueva | [G06] - [DOCUMENTACION] - Subir por PR la documentación del Sprint 1 (plan MVP, auditoría Skill Hub y DEVELOPMENT.md) | Documentación, Gestión | CP0 |
| HT04 | Nueva | [G06] - [FRONTEND] - Migrar las pantallas a /api/backoffice y retirar el parche del proxy | Frontend, Integración | CP1 |
| HU02 | Nueva | [G06] - [BACKEND] - Garantizar el orden estricto del outbox por clave de partición | Backend, Base de Datos | CP1 |
| HU02 | Nueva | [G06] - [DOCUMENTACION] - Documentar el orden por clave en el contrato del consumidor | Documentación | CP1 |
| HU11 | Nueva | [G06] - [BACKEND] - Definir interfaces y DTOs compartidos de reportes del Sprint 2 (contratos congelados) | Backend, Frontend, Integración | CP1 |
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Dar seguimiento al contrato con T10 (eventos, vidas agotadas y retención) y registrar su firma | Integración, Gestión | CP2 |
| HT04 | #1657 | [G06] - [FRONTEND] - Pantalla de administración: estado de 2FA y sesión | Frontend, Seguridad | CP3 |
| HU04 | Nueva | [G06] - [TEST] - Desarrollar tests con WireMock del cliente de proveedores (200, 404, 409, 503 y key enmascarada) | Testing, Integración | CP3 |
| HU05 | Nueva | [G06] - [TEST] - Desarrollar tests con WireMock de la activación de modelos (200, 409 y 503) | Testing, Integración | CP4 |
| HU08 | Nueva | [G06] - [REVISION] - Peer review de los consumidores de T07 y T05 | Testing | CP4 |
| HU12 | #313 | [G06] - [FRONTEND] - Diseñar vista de panel docente con semáforo de riesgo | Frontend, Diseño / UX-UI | CP4 |
| HU12 | #314 | [G06] - [TEST] - Desarrollar tests de RLS: docente A solo ve cohorte A | Testing, Seguridad, Base de Datos | CP4 |
| HU12 | #315 | [G06] - [TEST] - Validar regla anti-comparación y emisión de alerta | Testing, Seguridad | CP4 |
| HU09 | #302 | [G06] - [REVISION] - Peer review de manejo de streams y memoria y control en Taiga | Testing | CP5 |
| HU14 | #328 | [G06] - [FRONTEND] - Diseñar panel de configuración de umbrales y lista de alertas activas | Frontend, Diseño / UX-UI | CP5 |

## Mateo Carballo

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HT02 | Nueva | [G06] - [BACKEND] - Mergear el back-merge de la release v1.0.0 a develop (PR #58, trae la migración V18) | DevOps, Base de Datos | CP0 |
| HT02 | Nueva | [G06] - [BACKEND] - Cerrar las ramas superadas del backend (us-02-envelope y contratos-alineados-drive) | Gestión, DevOps | CP0 |
| HT04 | Nueva | [G06] - [BACKEND] - Cerrar la rama del frontend superada (fix/admin-export-service-spec) | Gestión, DevOps | CP0 |
| HU01 | Nueva | [G06] - [BACKEND] - Validar PAR-14 con la forma oficial y registrar PAR-12 (PR #56) | Backend, Base de Datos, Configuración | CP0 |
| HU01 | Nueva | [G06] - [TEST] - Desarrollar test de integración del servicio de parámetros con Testcontainers | Testing, Base de Datos | CP1 |
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Confirmar con T11 la materialización de notifications.events y el payload de los avisos | Integración, Gestión | CP2 |
| HU05 | Nueva | [G06] - [BACKEND] - Implementar cliente real de modelos evaluadores (listar, activo, desplegar, activar y borrar) | Backend, Integración | CP3 |
| HU05 | #276 | [G06] - [FRONTEND] - Implementar modal de conmutación con advertencia de impacto | Frontend, Diseño / UX-UI | CP3 |
| HU05 | Nueva | [G06] - [FRONTEND] - Conectar la pantalla de modelos al backend y quitar los datos en memoria | Frontend, Integración | CP3 |
| HU11 | #307 | [G06] - [DOCUMENTACION] - Documentar reglas de cálculo de riesgo y esquema del Read Model | Documentación, Análisis | CP3 |
| HU12 | #317 | [G06] - [REVISION] - Peer review de seguridad RLS y control en Taiga | Testing, Seguridad | CP3 |
| HT04 | Nueva | [G06] - [TEST] - Desarrollar specs de guards y de la vista de solo lectura (ADMIN, GESTOR y PROFESSOR) | Testing, Frontend, Seguridad | CP4 |
| HU06 | #3540 | [G06] - [FRONTEND] - Implementar pantalla de corridas de calibración | Frontend, Integración | CP4 |
| HU08 | Nueva | [G06] - [TEST] - Desarrollar tests de integración de consumidores (nuevo, duplicado, malformado a DLT y flag apagado) | Testing, Integración | CP4 |
| HU13 | #322 | [G06] - [FRONTEND] - Diseñar dashboard de KPIs con tarjetas CSAT y advertencia de anonimato | Frontend, Diseño / UX-UI | CP4 |

## Damián Baigorria

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Rehacer la solicitud de contratos a T02 (pertenencia docente, padrón y encuestas agregadas) | Integración, Gestión | CP0 |
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Redactar la primera solicitud de contratos a T05 (entregas y resultados prácticos) | Integración, Gestión | CP0 |
| HT02 | Nueva | [G06] - [BACKEND] - Mergear la release v1.0.0 a main y etiquetar la versión (PR #54) | DevOps, Gestión | CP0 |
| HU01 | Nueva | [G06] - [REVISION] - Revisar la PR #56 de alineación de parámetros con Skill Hub | Testing | CP0 |
| HU11 | #303 | [G06] - [BACKEND] - Crear migración Flyway y esquema del Read Model analítico por cohorte | Base de Datos, Backend | CP2 |
| HU01 | #3331 | [G06] - [FRONTEND] - Restringir edición de parámetros para el rol PROFESOR | Frontend, Seguridad | CP3 |
| HU11 | #304 | [G06] - [BACKEND] - Implementar algoritmo de clasificación de nivel de riesgo | Backend, Análisis | CP3 |
| HU12 | #312 | [G06] - [BACKEND] - Emitir STUDENT_AT_HIGH_RISK por outbox solo al pasar a riesgo alto | Backend, Integración | CP3 |
| HU07 | Nueva | [G06] - [REVISION] - Peer review de la fachada de veredicto y deriva | Testing | CP4 |
| HU13 | #325 | [G06] - [REVISION] - Peer review de privacidad y control en Taiga | Testing, Seguridad | CP4 |
| HU09 | #295 | [G06] - [BACKEND] - Implementar endpoint asíncrono de solicitud de exportación | Backend, Integración | CP5 |
| HU09 | #296 | [G06] - [BACKEND] - Implementar generador de archivos en streaming para CSV | Backend | CP5 |
| HU09 | #297 | [G06] - [BACKEND] - Implementar enlace temporal con vencimiento y validación de alcance por rol | Backend, Seguridad | CP5 |
| HU16 | Nueva | [G06] - [FRONTEND] - Implementar constructor de reportes con guardado de plantillas (WCAG AA) | Frontend, Diseño / UX-UI | CP5 |

## Joaquín Cortez

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HU05 | Nueva | [G06] - [REVISION] - Peer review de la fachada de modelos y del mapeo de errores | Testing | CP3 |
| HU06 | #3537 | [G06] - [BACKEND] - Implementar fachada del perfil de calibración institucional sobre T07 | Backend, Integración | CP3 |
| HU06 | #3538 | [G06] - [FRONTEND] - Implementar pantalla del perfil de calibración | Frontend, Integración | CP3 |
| HU15 | Nueva | [G06] - [BACKEND] - Implementar catálogo de métricas permitidas (lista blanca) con su disponibilidad | Backend, Análisis | CP3 |
| HU15 | Nueva | [G06] - [BACKEND] - Implementar plantillas y favoritas de reportes | Backend, Base de Datos | CP3 |
| HT04 | Nueva | [G06] - [REVISION] - Peer review de guards, migración de rutas, badge y 2FA | Testing | CP4 |
| HU06 | Nueva | [G06] - [DOCUMENTACION] - Documentar en OpenAPI la fachada de calibración | Documentación | CP4 |
| HU13 | #319 | [G06] - [BACKEND] - Implementar servicio de agregación analítica de indicadores de plataforma | Backend, Base de Datos | CP4 |
| HU13 | #320 | [G06] - [BACKEND] - Implementar mecanismo de protección de anonimato por umbral mínimo | Backend, Seguridad | CP4 |
| HU13 | #321 | [G06] - [BACKEND] - Implementar endpoint exclusivo ADMIN con regla anti-comparación | Backend, Seguridad | CP4 |
| HU13 | Nueva | [G06] - [BACKEND] - Aplicar el corte al cierre del curso y el reporte de abstenciones en los KPIs | Backend, Seguridad | CP4 |
| HU13 | #324 | [G06] - [DOCUMENTACION] - Documentar políticas de privacidad y fórmulas de agregación | Documentación, Seguridad | CP4 |
| HU15 | Nueva | [G06] - [DOCUMENTACION] - Documentar en OpenAPI métricas, plantillas y ejecución de reportes | Documentación | CP4 |
| HU14 | #331 | [G06] - [REVISION] - Peer review final y cierre en Taiga | Testing | CP5 |

## Valentina Maldonado

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HU10 | #291 | [G06] - [FRONTEND] - Diseñar badge de frescura en la cabecera de reportes | Frontend, Diseño / UX-UI | CP0 |
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Confirmar con T03 los valores posibles del resultado del desafío y registrar su firma | Integración, Investigación | CP2 |
| HT04 | Nueva | [G06] - [FRONTEND] - Ocultar en el dashboard los accesos no permitidos a GESTOR y PROFESSOR | Frontend, Seguridad | CP3 |
| HU08 | Nueva | [G06] - [BACKEND] - Implementar consumidores de llm.events (T07) y de T05 con flag, deduplicación y DLT | Backend, Integración, Configuración | CP3 |
| HU08 | Nueva | [G06] - [DOCUMENTACION] - Actualizar el mapeo de contratos de lectura con T07 y T05 | Documentación, Integración | CP3 |
| HU10 | Nueva | [G06] - [BACKEND] - Implementar cálculo de frescura al leer en cada reporte | Backend | CP3 |
| HU11 | #305 | [G06] - [BACKEND] - Implementar job programado de recálculo periódico | Backend, Base de Datos, Integración | CP3 |
| HT02 | Nueva | [G06] - [REVISION] - Peer review de concurrencia del outbox y del secreto del Gateway | Testing, Seguridad | CP4 |
| HU05 | Nueva | [G06] - [TEST] - Desarrollar specs de las pantallas de proveedores y modelos conectadas | Testing, Frontend | CP4 |
| HU07 | #284 | [G06] - [FRONTEND] - Diseñar indicador visual de deriva y banner de conmutación automática | Frontend, Diseño / UX-UI | CP4 |
| HU15 | Nueva | [G06] - [REVISION] - Peer review de seguridad del motor (RLS y lista blanca) | Testing, Seguridad | CP4 |
| HU09 | #299 | [G06] - [FRONTEND] - Diseñar diálogo de exportación y notificación de descarga | Frontend, Diseño / UX-UI | CP5 |

## Máximo Cerquatti

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HU03 | #3512 | [G06] - [FRONTEND] - Implementar guards de rutas de administración | Frontend, Seguridad | CP0 |
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Confirmar con T07 la llamada a /api/llm/admin/* y registrar las firmas de T07 y T01 | Integración, Seguridad, Documentación | CP2 |
| HU01 | Nueva | [G06] - [TEST] - Desarrollar tests de validación de PAR-14 y PAR-12 del registro | Testing | CP2 |
| HU04 | Nueva | [G06] - [BACKEND] - Implementar cliente HTTP hacia T07 con errores problem+json, timeout y reintento solo en lecturas | Backend, Integración, Seguridad | CP2 |
| HU05 | Nueva | [G06] - [DOCUMENTACION] - Registrar en el contrato que MODEL_CHANGED lo publica T07 | Documentación, Integración | CP2 |
| HU12 | #311 | [G06] - [BACKEND] - Aplicar Row Level Security por course_id y regla anti-comparación | Backend, Base de Datos, Seguridad | CP2 |
| HT02 | Nueva | [G06] - [BACKEND] - Verificar el secreto compartido del Gateway sin romper el entorno local | Backend, Seguridad, Configuración | CP3 |
| HU03 | Nueva | [G06] - [BACKEND] - Pasar el topic de auditoría de constante a propiedad tipada | Backend, Configuración | CP3 |
| HU11 | #308 | [G06] - [REVISION] - Peer review de modelado analítico y verificación en Taiga | Testing | CP3 |
| HU06 | Nueva | [G06] - [TEST] - Desarrollar tests con WireMock de la fachada de calibración | Testing, Integración | CP4 |
| HU07 | Nueva | [G06] - [TEST] - Desarrollar tests del estado de calibración del modelo activo | Testing, Integración | CP4 |
| HU15 | Nueva | [G06] - [TEST] - Desarrollar tests del motor (RLS, anti-comparación, anonimato y lista blanca) | Testing, Seguridad | CP4 |
| HU09 | #300 | [G06] - [TEST] - Desarrollar tests de generación, seguridad de alcance y vencimiento | Testing, Seguridad | CP5 |
| HU14 | #329 | [G06] - [TEST] - Desarrollar tests de evaluación de umbrales y autorización | Testing | CP5 |

## Regina Cerasulo

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HU04 | Nueva | [G06] - [BACKEND] - Implementar cliente real de proveedores y credenciales sin exponer la key | Backend, Integración, Seguridad | CP3 |
| HU04 | Nueva | [G06] - [FRONTEND] - Conectar la pantalla de proveedores al backend | Frontend, Integración | CP3 |
| HU04 | Nueva | [G06] - [DOCUMENTACION] - Documentar en OpenAPI la fachada de proveedores y modelos | Documentación | CP3 |
| HU12 | #310 | [G06] - [BACKEND] - Implementar endpoint del panel docente con validación de matrícula vía T02 | Backend, Seguridad | CP3 |
| HU02 | Nueva | [G06] - [TEST] - Desarrollar test de integración del orden del outbox con Testcontainers y Kafka | Testing, Integración | CP4 |
| HU06 | Nueva | [G06] - [REVISION] - Peer review de la fachada de calibración | Testing | CP4 |
| HU10 | Nueva | [G06] - [TEST] - Desarrollar tests del cálculo de frescura (14, 15 y 16 minutos) y del proyector de read models | Testing | CP4 |
| HU11 | #306 | [G06] - [TEST] - Desarrollar tests exhaustivos de partición de equivalencia del algoritmo de riesgo | Testing, Análisis | CP4 |
| HU13 | #323 | [G06] - [TEST] - Desarrollar tests de anonimato estadístico y autorización ADMIN | Testing, Seguridad | CP4 |
| HU14 | #326 | [G06] - [BACKEND] - Implementar modelo de umbrales y endpoints CRUD exclusivo ADMIN | Backend, Base de Datos | CP5 |
| HU14 | #327 | [G06] - [BACKEND] - Implementar evaluador periódico de métricas contra umbrales y despacho de alertas | Backend | CP5 |
| HU16 | Nueva | [G06] - [REVISION] - Peer review del constructor de reportes | Testing | CP5 |

## Bruno Gianoli

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HU06 | Nueva | [G06] - [BACKEND] - Archivar la rama del golden set local y borrarla (Opción A) | Gestión, DevOps | CP0 |
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Registrar la firma del contrato con T08 | Integración, Gestión | CP2 |
| HU04 | Nueva | [G06] - [REVISION] - Peer review de seguridad de credenciales y del cliente hacia T07 | Testing, Seguridad | CP3 |
| HU06 | #3539 | [G06] - [BACKEND] - Implementar fachada de corridas de calibración sobre T07 | Backend, Integración | CP3 |
| HU07 | Nueva | [G06] - [BACKEND] - Implementar endpoint del estado de calibración del modelo activo | Backend, Integración | CP4 |
| HU07 | #286 | [G06] - [DOCUMENTACION] - Documentar algoritmo de evaluación, umbrales PAR-14 y reglas de drift | Documentación, Análisis | CP4 |
| HU12 | Nueva | [G06] - [TEST] - Desarrollar specs del panel docente | Testing, Frontend | CP4 |
| HU15 | Nueva | [G06] - [BACKEND] - Implementar motor de consultas dinámicas con acceso por curso | Backend, Seguridad, Base de Datos | CP4 |
| HU15 | Nueva | [G06] - [BACKEND] - Implementar endpoint de ejecución de reportes (run) con plantilla o configuración | Backend, Seguridad | CP4 |
| HU16 | Nueva | [G06] - [TEST] - Desarrollar specs del constructor (crear, editar, correr plantilla y 403) | Testing, Frontend | CP5 |

## Ana Ducart

| Historia | Taiga | Tarea | Etiquetas | CP |
|---|---|---|---|---|
| HT01 | Nueva | [G06] - [DOCUMENTACION] - Crear una tarjeta de seguimiento por cada contrato con fecha tope el 02/10 | Gestión, Integración | CP0 |
| HT03 | Nueva | [G06] - [DOCUMENTACION] - Dejar Taiga coherente desde el CP0 y cargar el Sprint 2 | Gestión, Documentación | CP0 |
| HT03 | Nueva | [G06] - [DOCUMENTACION] - Diagramar las fuentes de datos hacia los reportes | Documentación, Diseño / UX-UI | CP2 |
| HT03 | Nueva | [G06] - [DOCUMENTACION] - Crear las páginas de wiki de G06 del Sprint 1 con la plantilla de la cátedra | Documentación, Diseño / UX-UI | CP3 |
| HT03 | Nueva | [G06] - [DOCUMENTACION] - Diagramar la secuencia del cambio de parámetro | Documentación, Diseño / UX-UI | CP3 |
| HU06 | Nueva | [G06] - [DOCUMENTACION] - Diagramar la secuencia ADMIN, Backoffice y T07 de la calibración | Documentación, Diseño / UX-UI | CP4 |
| HU12 | #316 | [G06] - [DOCUMENTACION] - Documentar en la wiki los endpoints del panel, la política RLS y el contrato de alertas | Documentación, Seguridad | CP4 |
| HT03 | Nueva | [G06] - [DOCUMENTACION] - Redactar el guion de la demo del Sprint 2 y el checklist E2E | Documentación, Gestión | CP5 |
| HU09 | #301 | [G06] - [DOCUMENTACION] - Documentar en la wiki el flujo asíncrono de exportación y el aviso EXPORT_READY | Documentación | CP5 |
| HU14 | #330 | [G06] - [DOCUMENTACION] - Documentar en la wiki el catálogo de umbrales y alertas | Documentación | CP5 |
| HU16 | Nueva | [G06] - [DOCUMENTACION] - Documentar en la wiki la vista del constructor y el catálogo de métricas | Documentación | CP5 |
