# SKILL — Build y tests

## Prerrequisitos

- JDK 21, Maven (o usar el wrapper `mvnw`), Docker (para Testcontainers).

## Compilar

```bash
mvn clean compile          # todo el monorepo
mvn -pl services/administration-service compile   # un módulo
```

## Probar

```bash
mvn test                              # unit + integración del módulo actual
mvn -pl services/administration-service test  # solo administration-service
mvn verify                            # incluye integración (Testcontainers)
```

- **Unitarias (JUnit 5 + Mockito):** reglas de dominio, validadores, casos de uso, autorización crítica.
- **Integración (Testcontainers):** API + DB (PostgreSQL real en contenedor), Flyway, Kafka (producción/consumo), Outbox, auth.

## Ejecutar el stack local

```bash
docker compose up -d          # eureka, config-server, gateway, servicios, postgres, kafka
mvn spring-boot:run           # o ejecutar la clase *Application de cada servicio
```

## Notas

- Mantené `open-in-view: false` y `ddl-auto: validate` (las migraciones las hace Flyway).
- Si un test de integración usa Kafka/Postgres, corre bajo Testcontainers; no "mockees" el broker para cubrir el caso.
- Verificá que el test que escribís cubre la regla de negocio (no solo el happy path).