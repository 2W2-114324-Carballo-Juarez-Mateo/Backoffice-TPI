# Documentación del Backoffice (Tema 12) — Carpeta para compartir y presentar

> Contenido generado para **compartir y presentar** el microservicio Backoffice (Tema 12) de la Plataforma de Aprendizaje Gamificado (UTN FRC · MSII). Abrí **`index.html`** en el navegador para navegar los flujos, o usá este README.

## Contenido

| Ruta | Qué es |
|---|---|
| **`index.html`** | Índice visual de todos los flujos (abrir en navegador) |
| **`flujos/*.html`** | Un HTML por flujo (diagrama mermaid autocontenido, renderiza al abrir) |
| **`DOCUMENTACION_MICROSERVICIO.md`** | Documentación consolidada del microservicio (arquitectura, servicios, contratos, parámetros, eventos, seguridad, endpoints, multitenancy, despliegue, planificación) |
| **`README.md`** | Este índice |

## Flujos disponibles

**Casos de uso**
- [Caso 1 · Login y sesión (Front → BFF → Identity)](flujos/casos-de-uso-Caso-1-Login-y-sesion-Front-BFF-Identity.html)
- [Caso 2 · Alta de administrador (gestión de plataforma)](flujos/casos-de-uso-Caso-2-Alta-de-administrador-gestion-de-plataforma.html)
- [Caso 3 · Cambio de configuración global (PAR-01) — Kafka + caché](flujos/casos-de-uso-Caso-3-Cambio-de-configuracion-global-PAR-01-Kafka-cache.html)
- [Caso 4 · Reporte por curso (multitenancy, RLS y caso ADMIN)](flujos/casos-de-uso-Caso-4-Reporte-por-curso-multitenancy-RLS-y-caso-ADMIN.html)
- [Caso 5 · Gestión del proveedor LLM (exclusivo ADMIN)](flujos/casos-de-uso-Caso-5-Gestion-del-proveedor-LLM-exclusivo-ADMIN.html)
- [Caso 6 · Panel, exportación y alertas (Reporting)](flujos/casos-de-uso-Caso-6-Panel-exportacion-y-alertas-Reporting.html)

**Comunicación entre microservicios**
- [Service Discovery (Eureka)](flujos/comunicacion-Service-Discovery-Eureka.html)
- [Comunicación síncrona (HTTP/REST)](flujos/comunicacion-Sincrona-HTTPREST.html)
- [Comunicación asíncrona (eventos Kafka)](flujos/comunicacion-Asincrona-Eventos-Kafka.html)
- [Flujo crítico · Gestión de proveedor de modelo (ADMIN)](flujos/comunicacion-Flujo-critico-gestion-de-proveedor-de-modelo-ADMIN.html)
- [Flujo crítico · Baja de ADMIN (Tema 01, consumido)](flujos/comunicacion-Flujo-critico-baja-de-ADMIN-del-Tema-01-consumido.html)
- [Cómo fluye un cambio de configuración](flujos/mensajeria-3-Como-fluye-un-cambio-de-configuracion.html)

**Diseño técnico por tarea**
- [Registro de parámetros — Diseño técnico](flujos/registro-parametros-4-Diseno-tecnico.html)
- [Administración de plataforma — Diseño técnico](flujos/administracion-plataforma-4-Diseno-tecnico.html)
- [Proveedor LLM — Diseño técnico](flujos/proveedor-llm-4-Diseno-tecnico.html)
- [Contratos de lectura — Diseño técnico](flujos/contratos-lectura-4-Diseno-tecnico.html)
- [Reportes docentes — Diseño técnico](flujos/reportes-docentes-4-Diseno-tecnico.html)

> Los diagramas se generan desde `plan/docs-site` (fuente de verdad). Los HTML son **autocontenidos**: solo necesitás conexión para cargar mermaid desde el CDN.