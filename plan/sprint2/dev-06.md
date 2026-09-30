# dev-06.md (Cerquatti, Máximo) — Tareas Sprint 2

> **Núcleo:** 2.898 líneas · **Condicionado y extra:** 990 · **Total techo:** 3.888 · **Repos:** BE + FE
> **Flujo:** `feature/tema-12-*` | `fix/tema-12-*` → `develop` · PR solo con la rama terminada · comentarios de review en GitHub
> **Fuente de verdad:** `distribucion-pareja.md` (reparto) y `tareas-sprint2.md` (contexto). Líneas = código + tests efectivos, estimadas (±30 %).

## Núcleo

| ID | Tarea | Capa | Líneas | CP |
|---|---|---|---:|---|
| #3512 | **La rama ya está pusheada** (`feature/tema-12-admin-route-guards`, `8c2c82e`): sincronizar con `develop`, abrir la PR y avisar a los dueños de las partes 03, 04, 06, 10 y 14 | FRONT | 98 | **CP0** |
| 04-T1 | Infraestructura del cliente HTTP a T07 (`RestClient` administrado, auth según C1, `problem+json` → excepciones de dominio, timeout 3 s, reintento solo en GET, `X-Request-Id`, 401/403 de T07 nunca como 500, stub como fallback con flag). Arrancás con WireMock sin esperar C1 | BACK | 750 | **CP2** |
| #311 | **Capa de acceso de reporting:** `ReportScopeResolver` + `TeacherMembershipPort` (adaptador T02 por Gateway con token de servicio, flag **fail-closed**) + `TenantContext` + **`V20__reporting_rls.sql`** con `ENABLE` **y `FORCE ROW LEVEL SECURITY`** + guardia anti-comparación. **V20 va sobre V19 de Damián** | BACK | 1.250 | **CP2** |
| 06B-T4 | Pasar el topic de auditoría (`TOPIC_AUDIT_EVENTS`, `DEFAULT_AUDIT_TOPIC`) de constante a propiedad tipada (el nombre `identity.audit.events` ya entró con la PR #47) | BACK | 100 | CP3 |
| 15-T5 | Tests del motor de US-15: RLS, anti-comparación, anonimato, lista blanca (métrica desconocida → 400) | TEST | 700 | CP4 |
| | **Subtotal núcleo** | | **2.898** | |

## Condicionado y extra

| ID | Tarea | Capa | Líneas | Condición |
|---|---|---|---:|---|
| 06B-T3 | Verificar `GATEWAY_SHARED_SECRET` con el mecanismo que acuerde T01 sin romper el entorno local | BACK | 160 | Gate: T01 |
| 06-T5 + 07-T4 | Tests WireMock de calibración y del estado de calibración | TEST | 580 | Gate: C1 |
| 14-T4 | Tests de alertas (HU14): en el límite, 1 punto abajo, no-ADMIN 403 | TEST | 250 | Extra |
| | **Subtotal** | | **990** | |

## Sin líneas de código (documentación y revisión)

- **C1:** dueño del contrato con T07 y de la fila de T01: confirmar `/api/llm/admin/*` (ruta, Gateway o Eureka, token, scopes), corregir el §6 como fachada y registrar que `MODEL_CHANGED` lo publica T07. Volcarlo a `CONTRATOS_T07_SOLICITUD.md` en la misma PR que las filas de firma.
- **Revisás:** #308 (modelado analítico) · S2-00 junto con Mateo: es tu contrato de `ReportScopeResolver`, revisalo con lupa.
- **Sale de tu lista:** 06B-T2 (IT del orden del outbox) pasó a Regina; vos la acompañás porque hiciste el test de publicación del Sprint 1.
- **Insumos para la wiki (Ana):** ejemplos de auditoría y de la política RLS.

## Archivos

- **Tuyos:** clientes de T07 (`configs/**/T07*`), `reporting/services/access/*`, `TenantContext`, `V20`, `GatewayTrustProperties`, el topic de los publishers de auditoría.
- **No los tocás:** el motor (Bruno) ni el panel (Regina): ellos llaman a `ReportScopeResolver`. Si ves que lo usan mal, comentario en su PR.

## Dependencias

- **Dependen de vos:** Regina (#310), Joaquín (HU13), Bruno (motor), Damián (export): por eso #311 va en el **CP2**.
- **Dependés de:** V19 (Damián, CP2) para las políticas RLS · S2-00.

## Puntos técnicos que te van a revisar

- La app se conecta como **dueña** de las tablas: sin `FORCE ROW LEVEL SECURITY` la política no aplica.
- `current_setting('app.current_course', true)` sin valor → **cero filas** (fail-closed), nunca "todas".
- `set_config(..., true)` solo vive en la transacción: el resolver corre dentro de `@Transactional` y el valor se pasa con parámetro, nunca concatenado.

## Te testean / revisan

04-T1 → Luciano (04-T4), revisa Bruno (04-T5) · #311 → Luciano (#314), revisa Mateo (#317) · 06B-T3/T4 → revisa Valentina (06B-T5) · #3512 → specs Mateo (05-N3), revisa Joaquín (05-N4).

> **DoD:** `mvn -B clean verify` / `npm run verify` en verde pegado en la PR · PR revisada según la matriz · Taiga movida por vos · RLS verificado en PostgreSQL real · OpenAPI y `docs/` al día en la misma PR.
