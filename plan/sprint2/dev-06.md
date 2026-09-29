# dev-06.md (Cerquatti, Máximo) — Tareas Sprint 2

> **Disponibilidad:** alta · **Núcleo:** 17 u · **Con gate:** 5 u · **Stretch:** — · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `tareas-sprint2.md`. Tamaños: S = 1, M = 2, L = 3, XL = 5 (relativos, no horas).

## Núcleo

| ID | Tarea | Capa | Tamaño | CP |
|---|---|---|---|---|
| #3512 | **La rama ya está pusheada** (`feature/tema-12-admin-route-guards`, `8c2c82e`): sincronizar con `develop`, abrir la PR y avisar a los dueños de las partes 03, 04, 06, 10 y 14 | FRONT | S | **CP0** |
| C1 | **Dueño del contrato con T07 (y fila de T01):** confirmar la llamada a `/api/llm/admin/*` (ruta, Gateway o Eureka, token y scopes) y volcarlo a `CONTRATOS_T07_SOLICITUD.md`; en la misma PR, **corregir el §6 como fachada** (ex C4) y registrar que `MODEL_CHANGED` lo publica T07 y el Backoffice deja de emitir `ModelProviderChanged` (ex 05-T5); completar las filas de **T07 y T01** en la tabla de firmas de `CONTRATOS.md` | DOC | M | CP0 → **CP2** |
| 04-T1 | Infraestructura del cliente HTTP a T07 (`RestClient` administrado, auth según C1, `problem+json` → excepciones de dominio, timeout 3 s, reintento solo en GET, `X-Request-Id`, 401/403 de T07 nunca como 500, stub como fallback con flag). Arrancás con WireMock sin esperar C1 | BACK | M | **CP2** |
| #311 | **Capa de acceso de reporting:** `ReportScopeResolver` + `TeacherMembershipPort` (adaptador T02 por Gateway con token de servicio, flag **fail-closed**) + `TenantContext` (`SET LOCAL app.current_course`) + `V19__reporting_rls.sql` con `ENABLE` **y `FORCE ROW LEVEL SECURITY`** + guardia anti-comparación | BACK | L | **CP2** |
| 06B-T4 | **Redefinida:** pasar el topic de auditoría (`TOPIC_AUDIT_EVENTS`, `DEFAULT_AUDIT_TOPIC`) de constante a propiedad tipada (el nombre `identity.audit.events` ya entró con la PR #47) | BACK | S | CP3 |
| 06B-T2 | IT del orden del outbox (Testcontainers + Kafka): falla v1 y v2 no sale antes (sobre 06B-T1 de Luciano) | TEST | M | CP4 |
| 15-T5 | Tests del motor de US-15: RLS, anti-comparación, anonimato, lista blanca (métrica desconocida → 400) | TEST | L | CP4 |
| 14-T4 | Tests de HU14 (si se hace el stretch): en el límite, 1 punto abajo, no-ADMIN 403 | TEST | M | CP5 |
| #308 | Peer review del modelado analítico (#303/#304/#305) | REV | S | CP3 |

## Con gate

| ID | Tarea | Capa | Tamaño | Gate |
|---|---|---|---|---|
| 06B-T3 | Verificar `GATEWAY_SHARED_SECRET` con el mecanismo que acuerde T01 sin romper el entorno local | BACK | S | T01 |
| 06-T5 | Tests WireMock de la fachada de calibración | TEST | M | C1 |
| 07-T4 | Tests del estado de calibración (**la parte de validación de PAR-14 no tiene gate**: hacela apenas Damián mergee 07-T1) | TEST | M | C1 |

## Revisiones que te tocan

- **S2-00** (Luciano) junto con Mateo: es tu contrato de `ReportScopeResolver`, revisalo con lupa.

## Fuera de tu lista (vs propuesta)

- **#323** (tests de anonimato de HU13) pasa a Joaquín: la propuesta te dejaba como "tester del equipo".
- **Entran a C1** las correcciones del contrato de T07 que antes eran de Ana (C4 y 05-T5): Ana no trabaja en el repo y es el mismo archivo que ya editás por C1.

## Insumos para la wiki (Ana)

Pasale ejemplos reales de request/response de auditoría y del panel (RLS), y revisá su sección antes de que la cierre.

## Archivos

- **Tuyos:** `configs/**/T07*` (cliente), `reporting/services/access/*`, `TenantContext`, `V19`, `GatewayTrustProperties`, publishers de auditoría (solo el topic).
- **No los tocás:** el motor (Bruno) ni el panel (Regina): ellos llaman a `ReportScopeResolver`. Si ves que lo usan mal, **comentario en su PR**.

## Dependencias

- **Dependen de vos:** Regina (#310), Mateo (HU13), Bruno (motor), Damián (export) → por eso #311 va en el **CP2**. Regina y Mateo dependen de 04-T1 para sus clientes reales.
- **Dependés de:** V18 (Damián, CP2) para las políticas RLS · S2-00.

## Te testean / revisan

#311 → Luciano (#314/#315), revisa Mateo (#317) · 04-T1 → Luciano (04-T4), revisa Bruno (04-T5) · 06B-T3/T4 → revisa Valentina (06B-T5) · #3512 → Mateo (05-N3), revisa Joaquín (05-N4).

## Puntos técnicos que te van a revisar

- La app se conecta como **dueña** de las tablas: sin `FORCE ROW LEVEL SECURITY` la política no aplica.
- `current_setting('app.current_course', true)` sin valor → **cero filas** (fail-closed), nunca "todas".
- `SET LOCAL` solo vive en la transacción: el resolver tiene que correr dentro de `@Transactional`.

> **DoD Nivel 0:** tarea terminada · `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos. **Nivel 1:** RLS verificado en PostgreSQL real, OpenAPI y docs al día.
