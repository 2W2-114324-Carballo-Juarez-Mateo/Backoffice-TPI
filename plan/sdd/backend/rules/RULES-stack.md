# RULES — Stack y convenciones de código

## Stack (obligatorio)

- **Java 21 (LTS)** + **Spring Boot 3** + **Maven** (multi-módulo).
- Persistencia con **JPA/Hibernate** (Spring Data JPA) sobre **PostgreSQL**.
- Mensajería con **Kafka** (Spring for Apache Kafka). **No** uses otro broker sin revisar el ADR-003.
- Migraciones con **Flyway**.
- API docs con **springdoc/OpenAPI 3**.
- Pruebas: **JUnit 5 + Mockito**; integración con **Testcontainers**.

## Estructura de capas (Clean Architecture)

- Flujo de dependencias: `api → application → domain ← infrastructure`.
- **Prohibido:** reglas de negocio en controllers; lógica de persistencia en `application`; dependencias externas en `domain`.
- Paquetes base: `com.backoffice.<servicio>.{api, application, domain, infrastructure}`.
- Comandos y consultas en `application/commands` y `application/queries` (patrón Command).
- DTOs en `api/dto` y `application/dto`; **nunca** exponer entidades de dominio directamente.

## Convenciones

- No agregues comentarios que repitan el código; solo cuando expliquen el "por qué".
- `@Transactional` solo donde hace falta; Open Session in View desactivado (`open-in-view: false`).
- Fechas en **UTC**.
- Errores: formato uniforme `{code, message, correlationId}`; nunca stack traces ni secretos.
- Cada endpoint con su autorización (ver `docs/05-endpoints.md`).
- Mantené la documentación de `sdd/` sincronizada con el código.