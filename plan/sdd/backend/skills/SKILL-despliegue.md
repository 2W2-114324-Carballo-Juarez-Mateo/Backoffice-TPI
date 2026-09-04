# SKILL — Despliegue y configuración

## Levantar el entorno local

```bash
docker compose up -d     # eureka · config-server · gateway · 5 servicios · postgres · kafka
```

- Kafka corre con la imagen `kafka:3-management` (topics, consumer groups y panel de gestión).
- PostgreSQL: **una base por servicio** (Database per Service).

## Migraciones (Flyway)

- Migraciones en `src/main/resources/db/migration/` (`V1__init.sql`, `V2__...`).
- `ddl-auto: validate` (Hibernate valida el esquema; no lo crea).
- Regla: las migraciones son **aditivas**; no modifiques migraciones ya aplicadas (creá una nueva).

## Configuración

- `application.yml`: valores no sensibles + placeholders (`${DATABASE_URL}`, `${KAFKA_BOOTSTRAP_SERVERS}`).
- Perfiles: `application-development.yml`, `testing`, `production` (`SPRING_PROFILES_ACTIVE`).
- Secretos por variables de entorno / secret manager (nunca en el repo).

## Registrar un servicio

- Dependencias: `spring-cloud-starter-netflix-eureka-client`, `spring-cloud-starter-config`.
- `application.yml`: `spring.application.name` + URL de Eureka y Config Server.
- Health checks: `/health/live`, `/health/ready` (readiness verifica dependencias críticas).

## Verificación rápida

```bash
docker compose ps                  # todos UP
curl http://localhost:8761/eureka  # discovery
curl http://localhost:8080/actuator/health  # gateway health
```

> Ver `docs/09-despliegue.md` para el detalle de estructura y repos.