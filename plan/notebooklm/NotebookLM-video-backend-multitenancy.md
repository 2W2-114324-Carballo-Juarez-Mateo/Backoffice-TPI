# NotebookLM — Video del backend + Multitenancy/RLS y Mensajería híbrida (versión corta)

> **Antes:** cargá la fuente `Backoffice-Fuente-NotebookLM.md`.
> **Cómo:** usá **Video Overview** y pegá este prompt.

---

## Prompt (corto)

Generá un **Video Overview** de la fuente "Backoffice-Fuente-NotebookLM" centrado en **la propuesta de backend del Backoffice (Tema 12)**. En español, didáctico, con **analogías** (gateway = portero, Kafka = buzón, consumidor puro = tablero de control, tenant = cada curso es su propio salón, caché con TTL = el pizarrón del aula que se actualiza cada 10 min o antes si llega el aviso).

**Estructura sugerida (5 bloques):**
1. **Qué es el Backoffice**: consumidor puro, 2 microservicios (Administration & Configuration → PAR-01..23 (PAR-24 asignado al Tema 01) + proveedor LLM exclusivo de ADMIN; Reporting & Analytics → reportes, panel, métricas CSAT, exportación, alertas); qué consume (T01/T02) y qué lee (02/04/05/07/08/10).
2. **Arquitectura e integración**: gateway de plataforma (T01), sync por gateway, autorización en 2 niveles, RF/RNF clave.
3. **Mensajería híbrida con Kafka + caché TTL**:
   - **Por qué híbrido**: REST responde (operaciones/consultas) y los eventos avisan (cambios de configuración global). **Kafka es la decisión de plataforma** (Notificaciones y Banco también lo usan), con topic + **consumer group por servicio**, **replay** disponible y **orden por partición**; RabbitMQ queda como alternativa (los read models de Reporting se reconstruyen por **contratos de lectura REST**).
   - **Flujo**: ADMIN cambia un PAR → Backoffice persiste + **Outbox** (misma transacción) y responde 200 → publica `GlobalConfigurationChanged` en el topic → cada consumidor actualiza su **caché local con TTL 10 min** (idempotencia por `event_id` y `version`).
   - **Resiliencia**: si el evento no llega, el TTL de 10 min es el respaldo; si Backoffice cae, se sirve el último valor conocido.
   - Es **infraestructura compartida** → se coordina con los equipos y se valida con la cátedra.
4. **Multitenancy + RLS (bloque central, explicalo bien)**:
   - **Por qué elegimos multitenancy lógico** (tenant = curso-cohorte / `course_id`): el doc oficial define la cohorte como el contexto compartido; los cursos son muchos y chicos; no hace falta base/esquema por tenant.
   - **Dónde aplica**: Reporting **tenant-scoped por `course_id`** (read models por cohorte) y Administration **global a propósito** (los PAR/proveedores valen igual en todos los cursos).
   - **TenantContext**: determina el alcance por operación desde el contexto validado (token + matrícula T02); **nunca confía en el `course_id` del request**.
   - **RLS (Row-Level Security) como refuerzo**: la base filtra por tenant con `current_setting('app.current_course')` → **no devuelve filas de otros tenants aunque falte el filtro** (defensa en profundidad). **RLS no reemplaza la autorización** (validar ≠ autorizar).
   - **Caso de prueba**: PROFESOR A → cohorte A → 200 · cohorte B → 403.
   - **Caso ADMIN (vistas entre cursos)**: el ADMIN ve su curso puntual (RLS normal) **y** todos los cursos vía el centinela `app.current_course = 'ALL'` (panel global, comparativas). RLS nunca se apaga (sin BYPASSRLS); quién puede setear 'ALL' lo decide la app desde el rol validado, nunca el request (PROFESOR con ALL → 403). Las lecturas globales se auditan.
   - **Convención para otros equipos**: clave común `app.current_course`, TenantContext validado, RLS por servicio.
5. **Cierre**: por qué la propuesta es sólida (consumidor puro, 2 servicios, contratos de lectura en sprint 1, mensajería híbrida resiliente con Kafka + caché, y aislamiento robusto con multitenancy lógico + RLS).

Mantené fidelidad a la fuente; no inventes requisitos.