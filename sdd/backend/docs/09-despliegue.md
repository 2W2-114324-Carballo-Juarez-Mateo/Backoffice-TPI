# 09 — Despliegue y estructura del repo

## Monorepo Maven multi-módulo

```text
backoffice-backend/
├── pom.xml                     ← POM padre
├── gateway/                    ← Spring Cloud Gateway
├── discovery-server/           ← Eureka
├── config-server/              ← Spring Cloud Config Server
├── services/
│   ├── administration-service/     ← com/backoffice/administration/...
│   └── reporting-service/          ← com/backoffice/reporting/...
├── building-blocks/
│   ├── contracts/              ← DTOs y contratos de eventos compartidos
│   ├── shared-kernel/
│   ├── observability/
│   └── security/
├── tests/
├── deploy/docker/
└── docker-compose.yml
```

## Estructura interna de un microservicio

```text
administration-service/src/main/java/com/backoffice/administration/
├── AdministrationApplication.java   ← @SpringBootApplication
├── api/        ← controllers (Parameter, ModelProvider, ModelFunction), dto, exception, config
├── application/ ← commands, queries, dto, validators, ports
├── domain/     ← model, events, exceptions, services
└── infrastructure/ ← persistence (JPA), messaging (outbox + kafka), external
```

## Docker Compose (local)

```text
discovery-server · config-server · gateway
administration-service · reporting-service
PostgreSQL (una base por servicio) · Kafka (management)
```

## Configuración

- `application.yml` con valores no sensibles; secretos por variables de entorno (`DATABASE_PASSWORD`, `JWT_SECRET`, `KAFKA_BOOTSTRAP_SERVERS`, `BREAK_GLASS_SECRET`).
- Migraciones con **Flyway** (`db/migration/`).

## Observabilidad (MVP)

- Health checks (`/health/live`, `/health/ready`), logs estructurados, correlation ID.
- Prometheus/Grafana/OpenTelemetry: **opcional según tiempo** (instrumentación preparada vía actuator).

> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§23-§27, §32).