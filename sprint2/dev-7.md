# dev-7.md (Cerquatti, Máximo) — Tareas del Sprint 2 ★ foco del PO

> **Capacidad:** 46,4 h · **Asignado:** 38 h (82 %) · **Capas:** BACK + FRONT + TEST + REV + DOC
> **Flujo:** `feature/tema-12-*` o `fix/tema-12-*` → PR a `develop` con 1 aprobación · sin push directo · commits del backend en español.
> **Flyway:** tenés reservada la versión **V19** (políticas de RLS de HU12).

## Tareas, en orden

| Día | ID | Tipo | Tarea | h | Depende de |
|---|---|---|---|---:|---|
| 1 | C1 | DOC | Confirmar con T07 y T01 la llamada a `/api/llm/admin/*`: ruta, Gateway o Eureka, token `client_credentials` o headers, y scopes. **Resolver la contradicción** entre el handoff ("sin M2M, directo por Eureka") y la skill `micro-to-micro-calls-with-a-service-token` | 2 | — |
| 1–2 | #3512 | FRONT | Guards: pushear `ecae5fd`, abrir la PR a `develop` y avisar a los dueños de las partes 03, 04, 06, 10 y 14 | 2 | — |
| 1–3 | 04-T1 | BACK | Infraestructura del cliente HTTP hacia T07: `RestClient` administrado (timeout 3 s, reintento solo en GET, `X-Request-Id` y `traceparent`), autenticación según C1, `problem+json` → `LlmProviderException`/`LlmModelException`, URL base en configuración tipada, stub como fallback con flag. Rama `feature/tema-12-llm-admin-client`, **merge el Día 3** | 6 | C1 |
| 3–4 | 06B-T2 | TEST | Test de integración del orden por `param_key`: falla v1 y v2 no sale antes (Testcontainers con Kafka) | 4 | 06B-T1 (Luciano) |
| 5–7 | 06B-T3 | BACK | Verificar `GATEWAY_SHARED_SECRET` (`GatewayTrustProperties`) con el mecanismo que acuerde T01, sin romper el entorno local | 3 | T01 |
| 5–7 | 06B-T4 | BACK | Configurar el topic de auditoría confirmado (C2) y la auditoría delegada en T01 (pantalla 11 sin 502) | 2 | C2, T01 |
| 6 | 06-T5 | TEST | Tests de integración con WireMock de la fachada de calibración (#3537 y #3539) | 4 | #3537, #3539 |
| 6–7 | 07-T4 | TEST | Tests del estado de calibración (07-T2) y de la validación de PAR-14 (07-T1) | 3 | 07-T1, 07-T2 |
| 6 | #308 | REV | Peer review del modelado analítico de HU11 (índices, eficiencia del job, reglas) | 2 | #303–#305 |
| 5–8 | #311 | BACK | RLS por `course_id` (`app.current_course`; `ALL` solo para ADMIN) y regla anti-comparación, migración `V19` | 6 | #303 |
| 8–9 | #323 | TEST | Tests de anonimato (4, 5 y 6 respuestas) y 403 para quien no es ADMIN | 4 | #319–#321 |

**Revisan tu trabajo:** Luciano testea el cliente T07 (04-T4, 05-T4) y Bruno lo revisa (04-T5); Mateo testea #3512 (05-N3) y Joaquín lo revisa (05-N4); Valentina revisa 06B-T3/T4 (06B-T5); Luciano testea #311 (#314) y Mateo lo revisa (#317).

## Checklist de DoD

- [ ] `mvn -B verify` en verde (Checkstyle, PMD, JaCoCo ≥ 90 %) · `npm run verify` en el frontend, sin `ng build`
- [ ] Tests de integración con Testcontainers · 200/403 por rol · **RLS verificado**
- [ ] PR revisada por otra persona · sin secretos (`GATEWAY_SHARED_SECRET` solo por variable de entorno) · OpenAPI y docs actualizados
- [ ] Tarjeta de Taiga movida por vos

## Registro de trabajo
<!-- Un bloque por tarea: estado, qué se hizo, archivos, decisiones, CA cubiertos, tests, PR/commits, deuda -->
