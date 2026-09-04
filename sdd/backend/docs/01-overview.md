# 01 — Overview del BackOffice

## Qué es

El **BackOffice** es el backend administrativo de la Plataforma Gamificada. Alcance oficial (puntos de los docentes):

1. **Administración de plataforma** — auth lado ADMIN, roles/permisos, gestión de ADMIN, configuración global, auditoría.
2. **Gestión del proveedor de modelo (exclusiva de ADMIN)** — proveedores y modelos de LLM, asignación modelo↔función, evaluador único, golden set, calibración, deriva.
3. **Reportes docentes** — reportes para PROFESOR/ADMIN.
4. **Panel de métricas de curso** (extra) — KPIs por curso.
5. **Exportación de datos** (extra) — resúmenes y reportes descargables.

**Fuera de alcance (otros equipos):** cursos, desafíos, usuarios (onboarding/avatar), gamificación, ranking, chat, notificaciones, encuestas de alumno, IA como producto. El BackOffice los **consume** vía eventos.

## Roles

| Rol | Alcance |
|---|---|
| ADMIN | Administración de plataforma, configuración y proveedores de modelo, auditoría, reportes globales |
| PROFESOR | Reportes y métricas de sus cursos |

## Stack

| Capa | Tecnología |
|---|---|
| Lenguaje | Java 21 (LTS) |
| Framework | Spring Boot 3 |
| Build | Maven (multi-módulo) |
| Gateway | Spring Cloud Gateway |
| Discovery | Eureka |
| Config | Spring Cloud Config Server |
| Mensajería | Kafka + Outbox (RabbitMQ = alternativa) |
| Persistencia | JPA/Hibernate · PostgreSQL (recomendada) |
| API docs | springdoc / OpenAPI 3 |
| Seguridad | Spring Security + JWT + 2FA (TOTP) |
| Pruebas | JUnit 5 · Mockito · Testcontainers · Spring Cloud Contract (opcional) |

## Reglas de negocio más importantes

- Nunca queda la plataforma **sin ADMIN activo** (incondicional).
- Un ADMIN **no puede auto-eliminarse**; baja reforzada (contraseña + 2FA + confirmación).
- **No existe hard delete** (baja lógica).
- Los cambios de configuración **rigen solo hacia adelante**.
- La **gestión de proveedores de modelo** es exclusiva de ADMIN (RF-IA-35).
- Las encuestas se consumen solo como **agregados anónimos** (RF-ENC-04).
- La auditoría es **inmutable**.

> Detalle: `rules/RULES-invariantes.md` · Fuente completa: `backoffice_backend_requerimientos_arquitectura.md`.