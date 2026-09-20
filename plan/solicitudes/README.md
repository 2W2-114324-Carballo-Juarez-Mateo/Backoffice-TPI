# Solicitudes de Contratos — Backoffice (Tema 12)

> Solicitudes de integración enviadas a cada equipo para definir los contratos. Estado actualizado en `plan/CONTRATOS.md`.

> **⚠️ Estándar de eventos (Drive oficial, 2026-09):** el envelope es **`EventoDTO{eventId, eventType, timestamp, producer, payload}`** (5 campos; `correlationId/actorId/role` → **headers de Kafka**), `eventType` en español, `producer = tema-XX-nombre`, topics en español (`cursos.ciclo-vida`, `desafios.resultados`, `sistema.notificaciones`). Las solicitudes que citan `occurredAt`/`correlationId`/`role` en el body quedan como **borradores a reconciliar en G1** (con T11/T01).

## Solicitudes por tema

| Archivo | Tema | Estado |
|---|---|---|
| [CONTRATOS_T01_SOLICITUD.md](CONTRATOS_T01_SOLICITUD.md) | **T01 · Usuarios/Identidad** | ✅ CERRADO (2 rondas) |
| [CONTRATOS_T08_RESPUESTA.md](CONTRATOS_T08_RESPUESTA.md) | **T08 · Banco** | 🟡 **ACUERDO** (REST + evento de saldo) |
| [CONTRATOS_T10_SOLICITUD.md](CONTRATOS_T10_SOLICITUD.md) | **T10 · Roadmap y Progreso** | 🟡 EN CURSO |
| [CONTRATOS_T09_SOLICITUD.md](CONTRATOS_T09_SOLICITUD.md) | **T09 · Mercado** | ⏳ SIN RESPONDER (nueva) |
| [CONTRATOS_T11_SOLICITUD.md](CONTRATOS_T11_SOLICITUD.md) | **T11 · Social y Notificaciones** | ⏳ SIN RESPONDER (nueva) |
| [CONTRATOS_T02_SOLICITUD.md](CONTRATOS_T02_SOLICITUD.md) | **T02 · Cursos y Matrícula** | ⏳ SIN RESPONDER (nueva — incluye **encuestas CSAT**) |
| [CONTRATOS_T03_RESPUESTA.md](CONTRATOS_T03_RESPUESTA.md) | **T03 · Motor de Desafíos** | 🟡 **ACUERDO** (hecho único) |
| [CONTRATOS_T07_SOLICITUD.md](CONTRATOS_T07_SOLICITUD.md) | **T07 · Evaluación LLM** | 🟡 **EN CURSO** (doc de ellos recibido + nuestra solicitud) |
| [CONTRATOS_T07_LLM_LIMITES.md](CONTRATOS_T07_LLM_LIMITES.md) | **T07 · Doc de ellos:** "Explicación de Límites y Uso" (4 capas, presupuesto USD 20/mes, alerta 70%→Backoffice) | 📥 REFERENCIA |

## Pendientes por generar
- **T05 · Prácticos** · (T07 ya tiene solicitud + doc recibido).
- **T04 · Teóricos/Encuestas:** **quedó sin lectura** — las **encuestas ahora son de Cursos (T02)**; no consumimos nada más de T04 por el momento.

> Registro consolidado: `plan/CONTRATOS.md` · Parámetros: `plan/PARAMETROS.md`.