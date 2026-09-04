# 08 — Marketplace de plugins TUP + agentes de IA

> Diseño **conceptual** (feature futura; fuera del MVP). Agentes objetivo: Claude, Codex, OpenCode, Gemini, Copilot.

## Capacidades

| Capacidad | Qué implica |
|---|---|
| **Publicar** | Manifesto (id, nombre, versión, autor, descripción, tags, permisos declarados), SDK del agente al que apunta |
| **Versionar** | Semver; versiones **inmutables**; canales stable/beta |
| **Descubrir** | Catálogo con búsqueda por agente/capacidad/rating; trust score |
| **Instalar/actualizar** | Resolución de dependencias, compatibilidad con la versión del agente, **rollback**, control de actualizaciones |
| **Validar** | Análisis estático, sandbox de ejecución, validación del esquema de **tools (MCP)**, firma + checksums |

## Seguridad

- **Least privilege:** el plugin declara permisos; el usuario aprueba explícitamente antes de ejecutar acciones.
- **Supply-chain:** artefactos firmados, checksums, proveniencia.
- **Sandbox** de ejecución y análisis de código (scan de secretos, anti-malware).
- **Cuotas / rate limits** por plugin y por usuario.
- **Auditoría** de instalación y ejecución.

## Agentes de IA y MCP

El plugin expone un **contrato de herramientas (tools)** — idealmente **MCP (Model Context Protocol)** — que el agente invoca con entradas/salidas tipadas. El marketplace valida ese esquema y el **set de permisos** antes de que el usuario confirme.

> Fuente: `frontend_plan_comunicacion.md` §7.