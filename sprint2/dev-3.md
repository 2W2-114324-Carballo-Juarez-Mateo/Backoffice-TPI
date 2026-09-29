# dev-3.md (Baigorria, Damián) — Tareas del Sprint 2 ★ foco del PO

> **Capacidad:** 31,5 h · **Asignado:** 27 h (86 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> **Flyway:** tenés reservada la versión **V18** (read model de HU11).

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 1 | C3 | DOC | Pedir a T02 la API de pertenencia docente por cohorte (HU12) y la fuente del CSAT (HU13) | 2 | — |
| 1–3 | #303 | BACK | Migración `V18__reporting_cohort_student_summary` y read model (course_id, student_id, días de actividad, tasa de aprobación, last_activity_at, risk_level) con índices. Rama `feature/tema-12-hu11-read-model`, **PR propia, merge el Día 3** | 6 | — |
| 3–5 | #304 | BACK | Algoritmo de riesgo con la regla de `uh/US-11.md`: **ROJO** más de 10 días o reprobación mayor a 60 %; **AMARILLO** entre 5 y 10 días o entre 40 y 60 %; **VERDE** aprobación ≥ 70 %. Umbrales en configuración tipada; "vidas agotadas" detrás de flag | 6 | #303 |
| 2–4 | 07-T1 | BACK | PAR-14 como fuente de la tolerancia: validar rango y formato en el registro, y comprobar que T07 lo lea con `backoffice.parameters.read` | 3 | — |
| 7–9 | #313 | FRONT | Panel docente con semáforo (color y texto, filtros por nivel, WCAG AA) | 5 | contrato de #310 |
| 8–9 | #315 | TEST | Tests de anti-comparación (la respuesta no trae datos de otros docentes) y de emisión de `StudentAtHighRisk` | 4 | #311, #312 |
| 8 | #325 | REV | Peer review de privacidad de HU13 (anonimato y sin comparación docente) | 1 | #319–#322 |

**Revisan tu trabajo:** Regina testea #303 y #304 (#306) y Máximo los revisa (#308); Máximo testea 07-T1 (07-T4); Joaquín testea #313 (12-T9).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] Tests de integración con Testcontainers donde haya BD o Kafka · 200/403 por rol · RLS donde aplique
- [ ] PR revisada por otra persona · sin secretos · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
