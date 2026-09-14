# Documentación del Microservicio — Backoffice (Tema 12)

> **Plataforma de Aprendizaje Gamificado de Programación y Desarrollo de Software** · UTN FRC · MSII · Grupo 2W2-G06 (Tema 12)
> Este documento consolida la documentación del microservicio para **compartir y presentar**. Las fuentes de verdad detalladas viven en `plan/` (repo Backoffice-TPI) y en el sitio desplegado (`backoffice-docs`). Los flujos en HTML están en `flujos/`.

---

## 1. Descripción general

El **Backoffice** es el **Tema 12** de la plataforma y se define como **consumidor puro**: no posee dominio operativo propio (no crea cursos, desafíos, usuarios ni economía), sino que **administra la configuración global, gobierna los modelos de IA y consume datos de otros temas** para ofrecer reportes, métricas y observabilidad.

| Dato | Valor |
|---|---|
| Servicios propietarios | **2**: `administration-service` (configuración + gobernanza LLM) y `reporting-service` (reportes, métricas, export, alertas) |
| Consume de | **T01** (identidad/auth/roles/auditoría/retención), **T02** (cohorte `course_id`, pertenencia docente) |
| Lee (contratos de lectura) | **T02, T04, T05, T07, T08, T10** (RF-RPT-10) |
| Provee | `GlobalConfigurationChanged` (PAR) a **T03** (montos) / **T09** (precios) / **T10** (rachas, pendiente) y `ModelProviderChanged` (evaluador) a **T07** |
| Stack | Java 21 · Spring Boot 3 · Kafka · PostgreSQL · Docker Compose |

---

## 2. Arquitectura

- **Estilo:** microservicios orientados a dominios + **Clean Architecture** por servicio (`domain → application → infrastructure → api`).
- **Comunicación:** **híbrida** — REST síncrono por el **API Gateway de plataforma (T01)** para lo síncrono; **Kafka** (con **Transactional Outbox**) para lo asíncrono. **Sin comunicación directa** entre microservicios (todo vuelve a pasar por el gateway).
- **Patrones:** Command/Query (CQRS), Specification, Adapter (un cliente por tema), Null Object, Decorator (auditoría), Observer + Outbox, Idempotency Key, caché con TTL 10 min en consumidores.
- **Service Discovery:** Eureka · **Config:** Config Server nativo · **Gateway local (demo):** `gateway-local` en `:8080`.

```
ADMIN / PROFESOR
   │
   ▼
API Gateway (T01)  ── valida JWT y propaga contexto (headers)
   │  /api/administration/**   →  administration-service (:8092)
   │  /api/reports/**           →  reporting-service    (:8093)
   │  /api/users/**             →  users-service (T01, consumido)
```

### Convención de rutas (contrato con T01)
Toda API pública vive bajo `/api/{servicio}/**` (sin prefijo → 404). Nuestros endpoints: `/api/administration/**` y `/api/reports/**` (incl. exportación y alertas bajo `/api/reports/**`).

---

## 3. Servicios propietarios

### 3.1 Administration & Configuration (`administration-service`, `:8092`)
- **Parámetros globales** `PAR-01..23` (registro genérico `GlobalParameter` con `value` jsonb y `version`), cambios **solo hacia adelante** (RF-CFG-06), **exclusivo ADMIN**, propagación por `GlobalConfigurationChanged` (Outbox + Kafka).
- **Gobernanza LLM (exclusiva ADMIN):** proveedores/modelos (API Keys cifradas), asignación modelo→función, **evaluador único activo** + calibración por **Golden Set** (`PAR-14`), detección de deriva y fallback. El Backoffice **NO invoca LLMs** (lo hace T07).
- **Alcance MVP:** el cambio de parámetro aplica **de inmediato** (sin fecha de vigencia futura).

### 3.2 Reporting & Analytics (`reporting-service`, `:8093`)
- **Ingesta de eventos** de los 6 temas con **deduplicación** (`event_id`) y DLT para malformados → **read models** en `reporting_db`.
- **Reportes:** panel del profesor (con **RLS** por `course_id` + alumno en riesgo), indicadores consolidados (CSAT con **bloqueo de anonimato**), exportación asíncrona (CSV/PDF), alertas configurables, **frescura ≤ 15 min**.
- **Reglas:** **sin comparación entre docentes** (RF-RPT-07) · encuestas **solo agregados anónimos** (RF-ENC-04/12) · ADMIN con alcance **global `ALL`**.

---

## 4. Contratos cross-team (estado)

> Registro completo y detalle en `plan/CONTRATOS.md` y las solicitudes en `plan/solicitudes/` (T01, T08, T10, T09, T11).

| Tema | Relación | Estado |
|---|---|---|
| **T01 · Usuarios** | Consume (auth/roles/auditoría/retención/cuentas) + provee auditoría | ✅ **CERRADO** |
| **T08 · Banco** | Consume (lectura REST: saldos/movimientos) · no consume PAR | 🟡 **ACUERDO PARCIAL** |
| **T10 · Roadmap** | Consume (progreso/XP/niveles) | 🟡 EN CURSO |
| **T09 · Mercado** | Provee (precios PAR-06/07) | 🟡 SOLICITUD LISTA |
| **T11 · Notificaciones** | Coordina convención de eventos + consumimos avisos | 🟡 SOLICITUD LISTA |
| **T02 · Cursos/Matrícula** | Consume (cohorte, pertenencia, `RosterUpdated`) | ⏳ PENDIENTE |
| **T04 · Teóricos/Encuestas** | Consume (agregados anónimos CSAT) | ⏳ PENDIENTE |
| **T05 · Prácticos** | Consume (entregas) + provee PAR-19/20 | ⏳ PENDIENTE |
| **T07 · Evaluación LLM** | Consume (deriva/calibración) + provee `ModelProviderChanged` | ⏳ PENDIENTE |
| **T03 · Desafíos** | Provee PAR-01/03 (deriva montos) + lectura de métricas | ⏳ PENDIENTE |

### Resumen de los contratos cerrados con T01
- **JWT:** claims `sub`, `roles[]`, `type`, `jti`, `sid`, `est`, `pwd`, `onb`, `iat`, `exp` · RS256 · JWKS `/.well-known/jwks.json` · access ~10 min · **el gateway valida, Backoffice no valida firma**.
- **Contexto (headers):** `X-Principal-Type`, `X-User-Id`, `X-Service-Id`, `X-User-Roles`, `X-Service-Scopes`, `traceparent`, `X-Request-Id`. **No hay `X-Course-Id`** (alcance derivado server-side).
- **Roles reales:** `ADMIN` / `PROFESOR` / `ALUMNO` (+ `MS` service-to-service). **No existe `AUDITOR`**. Autorización **local** con `@PreAuthorize` (no hay endpoint REST de autorización).
- **Auditoría:** publicamos en `audit.events` (v1) con envelope + `role`; lectura `GET /api/users/audit` (ADMIN, paginado).
- **Retención:** no purgamos por nuestra cuenta; alineamos read models ante `DataAnonymized`/`RetentionDecisionCreated`.
- **Cuentas ADMIN:** 100% de T01 (`/api/auth/*`, `/api/admin/accounts/*`); último ADMIN y baja 2FA validadas en users-service.
- **PAR-24:** ownership T01. **Trazabilidad:** `traceparent` + `X-Request-Id`.

---

## 5. Parámetros globales

> Registro completo en `plan/PARAMETROS.md`.

- **PAR-01..18** ✅ confirmados (economía, tabla del PRD / RF-CFG-04): XP, monedas, vidas, calibración, retención, mínimo de encuesta, etc.
- **PAR-19..23** 🟡 candidatos a validar con la cátedra (penalidad tardía, multiplicador, límite IA, frescura).
- **PAR-24** 🔵 externo (T01).
- Consumidores de la economía: **T03** deriva montos (PAR-01/03) · **T09** arma catálogo (PAR-06/07) · **T10** rachas (PAR-21, pendiente). **Banco no consume PAR** (registra montos ya resueltos).

---

## 6. Eventos y topics (Kafka)

| Topic | Rol Backoffice | Eventos |
|---|---|---|
| `administration.events` | **Publica** | `GlobalConfigurationChanged`, `ModelProviderChanged`, `ModelFunctionChanged` |
| `audit.events` (v1) | **Publica** | `ParameterChanged`, `ModelProviderChanged`, `EvaluatorActivated`, `GlobalRead` |
| `identity.events` / `retention.events` | T01 (consumido) | payloads pendientes de contrato |
| `course.events`, `survey.events`, `ranking.events`, `bank.events`, `roadmap.events`, `challenge.events` | **Consume** | read models de Reporting |

**Envelope estándar:** `{eventId, eventType, occurredAt, correlationId, actorId, role, source, payload}` · **idempotencia** por `event_id` + versión · **Outbox** en la misma transacción · **caché TTL 10 min** en consumidores.

---

## 7. Seguridad

- **Validar ≠ autorizar:** gateway valida; cada servicio autoriza localmente con el rol propagado.
- **Multitenancy + RLS:** `TenantContext` setea `app.current_course` desde el contexto validado (nunca del request); RLS refuerza; el ADMIN global usa el centinela `ALL` (server-side, auditado, **sin** `BYPASSRLS`).
- **Secretos:** nunca en repo/logs/respuestas; API Keys cifradas y enmascaradas.
- **Rate limiting** en el gateway (Bucket4j/Redis) con `429` + `Retry-After` + Idempotency Keys.

---

## 8. Despliegue

- **Docker Compose** (`Repositorio/TPI---Backoffice-Demo-/.compose/docker-compose.yml`): eureka `:8761`, config `:8888`, gateway `:8080`, admin `:8092`, reporting `:8093`, PostgreSQL ×2, Kafka `:9092`.
- **Front:** SPA Angular (9 vistas) — [GitHub Pages](https://2W2-114324-Carballo-Juarez-Mateo.github.io/TPI---Backoffice-Demo-/).
- **Docs:** sitio VitePress desplegado en [backoffice-docs](https://2W2-114324-Carballo-Juarez-Mateo.github.io/backoffice-docs/).

---

## 9. Planificación

- **Estructura:** 2 temas estratégicos (TH-01 Gobernanza / TH-02 Observabilidad) → **5 épicas** (EP-01..05) → **14 historias** (US-01..14).
- **Estimación:** historia = SP (Fibonacci) · tarea = horas · convención `[G06] - [ROL] -`.
- **Backlog listo para Taiga:** `plan/tareas.md` (104 tareas). ⚠️ US-06 (golden set) **BLOQUEADA** hasta definir contrato con T07.
- **Prioridad:** Must = US-01..08 y US-10 (43 SP) · Should = US-11..14 · Could = US-09.

---

## 10. Flujos (HTML)

Cada flujo está en **`flujos/`** como HTML autocontenido (mermaid). Ver `README.md` o `index.html`. Fuente de los diagramas: `plan/docs-site` (casos de uso, comunicación, mensajería y diseño técnico por tarea).

> **Fuentes de verdad:** `plan/backoffice_backend_requerimientos_arquitectura.md` (doc fuente) · `plan/sdd/backend/` (SDD) · `plan/CONTRATOS.md` · `plan/PARAMETROS.md` · `plan/tareas.md`.