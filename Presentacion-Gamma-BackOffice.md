# Presentación — BackOffice (Tema 12) para Gamma

> **IMPORTANTE — IDIOMA (leelo primero):** Generá la presentación **100% en español** (español rioplatense, tono académico y formal). TODOS los títulos, subtítulos, bullets, tablas, llamados y notas de orador deben quedar en español; no traduzcas nada a otro idioma. En Gamma, además, elegí **Español** en el selector de idioma antes de generar.

> **Cómo usar en Gamma:** pegá TODO este documento en Gamma → "Generar a partir de texto". Usá el tema limpio, tono académico, **10 slides** (una por cada `## Slide N`). Gamma genera una diapositiva por sección.

---

## Slide 1 — Portada

**BackOffice · Tema 12**

Plataforma de Aprendizaje Gamificado de Programación

Trabajo Integrador · 4° Cuatrimestre

**Rol del equipo: BFF por experiencia**

Nginx · BFF · API Gateway · Kafka · Multitenancy + RLS · Despliegue

---

## Slide 2 — Definición de roles por grupo

La cátedra pidió definir qué rol asume cada equipo:

| Rol (de la consigna) | Quién lo asume | Estado |
|---|---|---|
| **Implementación del BFF** | Equipo BackOffice (BFF por experiencia) | ✅ Asumido |
| **Librería compartida** (@tup/ui, @tup/contracts) | A coordinar en clase | ⏳ Proponemos aportar los contratos OpenAPI |
| **Marketplace de skills** | A coordinar en clase | ⏳ El BackOffice lo consume (config de agentes) |

**Postura:** asumimos el BFF por experiencia y proponemos un **estándar de BFF de plataforma**: una respuesta por pantalla · cookie → contexto · sirve al SSR · sin reglas de negocio.

---

## Slide 3 — Qué hacemos como BackOffice

- Somos el **"tablero de control"** de la plataforma: no fabricamos los datos de juego, los leemos de los demás equipos (**consumidor puro**).
- **Administramos la configuración global**: parámetros de economía (PAR-01..24) y proveedores de IA — una decisión que vale para todos los cursos.
- **Mostramos reportes y métricas** por curso, aislados entre sí (cada curso es su propio salón).
- **Acceso ordenado**: el front entra por Nginx → BFF → Gateway; nunca toca la base ni el broker.

---

## Slide 4 — Arquitectura general (por capas)

- **FRONT**: Navegador → Nginx → App Angular SSR · BFF BackOffice.
- **BACK**: API Gateway (T01) → Administration & Configuration · Reporting & Analytics.
- **MENSAJERÍA**: Kafka (eventos de configuración, Outbox, idempotencia).
- **BD**: PostgreSQL por servicio · read models tenant-scoped (course_id) + RLS.

Regla: **sync por el Gateway (T01)** y **asíncrono por Kafka** con Outbox + idempotencia.

---

## Slide 5 — Frontend: Caso A (Angular SSR + Nginx + BFF)

- Apps Angular SSR **independientes por dominio** en multirepos, integradas por **Nginx** bajo un mismo dominio (`/backoffice`, `/api/*`…). **Por qué**: autonomía entre equipos y aislamiento de fallas.
- **Analogía del edificio**: Nginx = recepcionista · BFF = mesero · Gateway = guardia · microservicios = oficinas.
- **BFF BackOffice**: junta lo que pide la pantalla en una sola respuesta y sirve al SSR. **Sin reglas de negocio**; el front nunca habla con la base ni con Kafka.
- **Despliegue**: Docker 2 etapas (`node:20` → `nginx:alpine`) + `envsubst` + CI/CD (GitHub Actions) + Rolling Update y Feature Flags.

---

## Slide 6 — Cómo nos conectamos con los microservicios

El front nunca habla directo: `Front → BFF → API Gateway (T01) → microservicios`.

| Servicio | Qué usa la UI admin |
|---|---|
| **API Gateway (T01)** | Única puerta de /api: JWT, 2FA, rate limiting |
| **Identity (T01)** | Login, sesión (cookie httpOnly), roles, 401 → login |
| **Administration & Configuration** | PAR-01..24, proveedores LLM, evaluador, golden set |
| **Reporting & Analytics** | Panel, reportes docentes, métricas/CSAT, export, alertas |
| **Cursos / Matrícula (T02)** | Listar cursos (selector de tenant) y validar pertenencia |
| **Lecturas 02/04/05/07/08/10** | Solo si el panel lo requiere (progreso, ranking, encuestas) |

---

## Slide 7 — Multitenancy + RLS (cada curso es su salón)

- **Tenant = curso-cohorte (`course_id`)**: multitenancy lógico (una base por servicio + columna course_id).
- **TenantContext**: setea `app.current_course` desde el contexto validado (token + matrícula **T02**) — **nunca del request**.
- **RLS como refuerzo**: la base no devuelve filas de otros cursos aunque el query olvide el WHERE.
- **Caso ADMIN global**: curso puntual o **ALL** (centinela); sin BYPASSRLS; PROFESOR con ALL → 403; lecturas globales auditadas.
- **Prueba de aislamiento**: PROFESOR A → curso A → 200 · curso B → 403 · ADMIN → panel global → 200.

---

## Slide 8 — Mensajería híbrida con Kafka

- **REST responde; los eventos avisan**: REST por el gateway (síncrono) + Kafka solo para notificar cambios de configuración.
- **Kafka = decisión de plataforma**: Notificaciones y Banco también lo usan (RabbitMQ queda como alternativa).
- **Outbox**: el evento se escribe en la misma transacción que el cambio → no se pierde.
- **Idempotencia**: por `event_id` y por `version` (el consumidor descarta v ≤ local).
- **Caché con TTL 10 min**: los consumidores (T03/05/08/10) guardan el valor; si el evento no llega, el TTL es el respaldo.
- **Replay disponible**: los read models igual se reconstruyen por contratos REST.

---

## Slide 9 — Flujos y casos de uso (TPI)

1. **Login + 2FA** → BFF → Identity (**T01**): cookie httpOnly, 401 → login.
2. **Gestión de plataforma** → Administration con **T01** (roles, auditoría).
3. **Cambio de PAR-01** → REST + Outbox + Kafka + caché TTL: **T03/05/08/10** lo aplican hacia adelante (RF-CFG-06).
4. **Reporte por curso (RLS)** → Reporting tenant-scoped; pertenencia con **T02**; caso ADMIN ALL.
5. **Proveedor LLM** → exclusivo ADMIN; **T07** lo consume (`ModelProviderChanged`).
6. **Exportación y alertas** → Reporting + lecturas de **T02/04/05/07/08/10**.

> Todos los flujos pasan por el **API Gateway (T01)** y usan **Kafka + Outbox + idempotencia**.

---

## Slide 10 — Integración con otros equipos y cierre

| Otro equipo | Qué da / qué recibe |
|---|---|
| **T01 Identidad** | Nos da: login, roles, 2FA, gateway, auditoría |
| **T02 Matrícula** | Nos da: pertenencia a la cohorte (base del tenant) |
| **T03/05/08/10** | Reciben: la configuración de PAR |
| **T04/05/07/08/10** | Nos dan: contratos de lectura para reportes |
| **T07 Evaluación LLM** | Recibe: el proveedor LLM (exclusivo ADMIN) |

**Próximos pasos:** validar el broker (Kafka) y los contratos con la cátedra · coordinar estándar BFF, librería compartida y marketplace · soporte visual interactivo en el sitio.

**¡Gracias!**

---

> **Instrucción final:** si Gamma pide regenerar o completar alguna sección, mantené **todo en español** (mismo tono académico). No uses otro idioma en ningún texto de la presentación.