# 06 — Patrones de diseño aplicados

Mapa de patrones con el lugar concreto. Paquetes bajo `com.backoffice.<servicio>`.

## Domain

- **Specification** → `identity.domain.specifications` (`AtLeastOneActiveAdminSpec`), `administration.domain.specifications` (reglas de cambio de parámetro/proveedor). Reglas compuestas y testeables (RF-ROL-05, RF-CFG-06, RF-IA-35).
- **Strategy** → `administration.domain.retention` (`RetentionDecisionStrategy`: extender/anonimizar). Política intercambiable (RF-RET-03).
- **Null Object** → `administration.infrastructure.external` (`ProviderRegistryClient` + `NoOpProviderClient`). Fallback ante caída de servicios externos (RNF-06).

## Application

- **Command** → `*.application.commands` (`UpdateParameterCommand`, `RegisterModelProviderCommand`, `AssignModelToFunctionCommand`, `DeleteAdminCommand` + handlers). Casos de uso aislados del HTTP.
- **Chain of Responsibility** → `identity.application.validation` (`AdminDeletionValidator`: auto-eliminación → 2FA → confirmación → último ADMIN). (RF-ROL-06)
- **Decorator** → `audit.application` (`AuditableCommandDecorator`). Auditoría transversal sin ensuciar el caso de uso (RF-AUD-01/03).

## Infrastructure

- **Adapter** → `*.infrastructure.external` (`IdentityClient`, `AIProviderClient`, `CourseClient`). Aísla red/DTOs.
- **Repository** → `*.infrastructure.persistence` (Spring Data JPA).
- **Observer + Outbox** → `*.domain.events` + `*.messaging.outbox`. Entrega de eventos en la misma transacción.
- **Unit of Work** → servicio `@Transactional` que persiste entidad + `OutboxMessage` en el mismo commit.
- **Idempotency Key** → `*.api.filter` / `*.application.commands`. Operaciones críticas (PUT config, proveedores, baja de ADMIN) responden el resultado original ante duplicados.

## Tabla resumen

| Patrón | Paquete | RF |
|---|---|---|
| Specification | `identity.domain.specifications` · `administration.domain.specifications` | RF-ROL-05 · RF-CFG-06 |
| Strategy | `administration.domain.retention` | RF-RET-03 |
| Null Object | `administration.infrastructure.external` | RNF-06 |
| Command | `*.application.commands` | capas |
| Chain of Responsibility | `identity.application.validation` | RF-ROL-06 |
| Decorator | `audit.application` | RF-AUD-01/03 |
| Adapter | `*.infrastructure.external` | cross-team |
| Observer + Outbox | `*.domain.events` · `*.messaging.outbox` | eventos |
| Unit of Work | `@Transactional` | outbox |
| Idempotency Key | `*.api.filter` | 429/duplicados |

> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§25bis).