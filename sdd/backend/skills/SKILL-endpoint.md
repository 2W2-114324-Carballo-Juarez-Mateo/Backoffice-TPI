# SKILL — Agregar un endpoint (de punta a punta)

Ejemplo: `PUT /api/administration/parameters/PAR-01` en **administration-service**.

## 1. Api — Controller + DTO

- `api/controllers/ParameterController.java`: método `PUT /api/administration/parameters/{key}`.
- DTO de request/response en `api/dto` (validaciones con Jakarta Validation).
- Anotá la autorización mínima (`@PreAuthorize("hasRole('ADMIN')")`); el **alcance** se valida en aplicación.

## 2. Application — Command

- `application/commands/UpdateParameterCommand.java` + `UpdateParameterCommandHandler.java`.
- El handler orquesta: valida el rol → valida la **regla de negocio** (versión + hacia adelante, RF-CFG-06) → persiste el cambio → registra el **Outbox event** → devuelve resultado.

## 3. Domain — regla

- Si la regla es compuesta (ej. cambio de proveedor con calibración), usá el patrón **Specification** (`domain/specifications`). No pongas la lógica en el controller.

## 4. Infrastructure — persistencia + evento

- Repositorio JPA para `GlobalParameter`.
- Si el cambio debe notificar (ej. `GlobalConfigurationChanged`), escribí `OutboxMessage` en la misma transacción (ver `SKILL-evento.md`).

## 5. Documentación y tests

- Endpoint queda documentado por springdoc/OpenAPI (anotaciones en el controller).
- Test unitario del handler + test de integración (MockMvc + Testcontainers).
- Actualizá `docs/05-endpoints.md` (matriz endpoint → rol → alcance).

> **Recordá:** autorización siempre en el servicio, errores con formato uniforme, y sync de `sdd/`.