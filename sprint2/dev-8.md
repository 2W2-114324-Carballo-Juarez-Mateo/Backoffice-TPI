# dev-8.md (Cerasulo, Regina) — Tareas del Sprint 2 ★ foco del PO

> **Capacidad:** 35,3 h · **Asignado:** 30 h (85 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> **Continuidad:** hiciste S4 (proveedores). Ahora pasás del stub al cliente real sobre la infraestructura de Máximo (04-T1).

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 2–5 | 04-T2 | BACK | Cliente real de `LlmProviderClient`: providers, provider-credentials (alta y baja), discover-models, test-model. La key viaja a T07 en `secrets` (solo escritura); nunca se loguea ni se devuelve. Rama `feature/tema-12-llm-providers-models-real` | 5 | 04-T1 (Máximo) |
| 5 | 04-T6 | DOC | OpenAPI de la fachada de proveedores y modelos | 2 | 04-T2, 05-T1 |
| 4–6 | 04-T3 | FRONT | Conectar la pantalla 09 al backend: quitar los datos en memoria y pasar de `/api/administration/llm/providers` a `/api/backoffice/llm/...` | 4 | 04-T2 |
| 5–6 | #306 | TEST | Tests del algoritmo de riesgo: partición de equivalencia y valores límite (10/11 días, 4/5 días, 40/60 %, 70 %) | 5 | #304 |
| 6 | 06-T7 | REV | Peer review de la fachada de calibración (#3537 y #3539) | 1 | HU06 |
| 5–8 | #310 | BACK | `GET ${app.api.private-path}/reports/courses/{courseId}/teacher` con un puerto de pertenencia docente (adaptador de T02 según C3; si no hay respuesta, flag). No pertenece → 403 | 6 | #303, C3 |
| 6–8 | #320 | BACK | Anonimato por umbral mínimo (valor en configuración; verificar en `PARAMETROS.md` si es un PAR, porque hay conflicto con PAR-18) | 4 | #319 |
| 7–8 | #1657 | FRONT | Estado de 2FA y sesión en la pantalla de administración (depende de que T01 lo exponga; si no, pasa a "Necesita información") | 3 | T01 |

**Revisan tu trabajo:** Luciano testea 04-T2 (04-T4) y Bruno lo revisa (04-T5); Valentina testea la pantalla 09 (05-T7); Luciano testea #310 (#314) y Mateo lo revisa (#317); Máximo testea #320 (#323).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] Ninguna API key en logs, respuestas ni en el repo · 200/403 por rol · RLS en #310
- [ ] PR revisada por otra persona · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
