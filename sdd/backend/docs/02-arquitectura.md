# 02 — Arquitectura del Backend

## Estilo

**Microservicios orientados a dominios** (bounded contexts), no un microservicio por entidad. Cada servicio con Clean Architecture (`api → application → domain ← infrastructure`) y su propia base (**Database per Service**). El Backoffice (Tema 12) es **consumidor puro** con **2 servicios propietarios**.

## Diagrama

```text
Frontend (por definir) → API Gateway de PLATAFORMA (Tema 01)
                              ├── Administration & Configuration Service → administration_db
                              └── Reporting & Analytics Service → reporting_db

Consume: Tema 01 (identidad/auth/roles/auditoría) · Tema 02 (cohorte)
Lectura: 02/04/05/07/08/10 (contratos de lectura)

Infra: Eureka (discovery) · Config Server · Kafka (eventos) · PostgreSQL por servicio
```

## Principios

- **Consumidor puro:** el Backoffice no es dueño de identidad, cohorte, desafíos ni economía; consume/lee por **contratos de lectura**.
- **Toda llamada síncrona entre servicios pasa por el gateway** (regla no negociable; no hay comunicación directa).
- **Validar ≠ autorizar:** el gateway valida el token; la autorización la toma el servicio dueño de la regla.
- **Comunicación:** REST por el gateway (síncrono) + **Kafka** (asíncrono) con **Outbox** e **idempotencia**.
- **Correlation ID** propagado en todo el recorrido.
- **Rate limiting** en el gateway (429 con `Retry-After`) + Idempotency Keys en operaciones críticas.

## Service Discovery (Eureka)

Los microservicios se registran al iniciar; el Gateway consulta a Eureka la ubicación de la instancia activa antes de enrutar (sin direcciones fijas).

## Multitenancy y RLS

- **Tenant = curso-cohorte (`course_id`)**. Multitenancy **lógico** (base compartida + columna tenant) en las tablas tenant-scoped de **Reporting** (read models por cohorte). **Administration es global a propósito** (PAR/proveedores valen igual en todos los cursos).
- **TenantContext**: determina `course_id`/rol/pertenencia por operación desde el contexto validado (token + matrícula T02); **nunca confía en el `course_id` del request**.
- **RLS (PostgreSQL) como refuerzo**: política por tabla tenant-scoped con `USING (course_id = current_setting('app.current_course')::uuid)`. La base no devuelve filas de otros tenants aunque falte el filtro. **RLS no reemplaza la autorización** (*validar ≠ autorizar*).
- **Caso ADMIN (vistas entre cursos)**: dos alcances — **puntual** (`course_id`, RLS normal) y **global** (`'ALL'`, centinela: panel general y comparativas). RLS **nunca se desactiva** (sin `BYPASSRLS`); la política incluye `OR current_setting('app.current_course') = 'ALL'`. Quién setea `'ALL'` lo decide la app (rol ADMIN validado o permiso `REPORTS_VIEW_ALL`), nunca el request → PROFESOR con ALL → 403. Lecturas globales auditadas.
- Caso de prueba obligatorio: PROFESOR A → cohorte A → 200 · cohorte B → 403 · intento ALL → 403 · ADMIN → panel global → 200.
- Convención de plataforma (para otros equipos): clave `app.current_course` común, TenantContext desde contexto validado, RLS por servicio.

## Roles de los componentes

| Componente | Rol |
|---|---|
| API Gateway | **De plataforma (Tema 01)**: única puerta, JWT, rate limiting, correlation ID. Sync entre servicios por acá |
| Eureka | Registro + health + ubicación de instancias |
| Config Server | Configuración no sensible, perfiles por ambiente |
| Kafka | Eventos de dominio, read models, auditoría (T01 persiste) |

> Fuente: `backoffice_backend_requerimientos_arquitectura.md` (§6-§11) · `TUP_PIV_BE_PROPUESTA_ARQ.pdf`.