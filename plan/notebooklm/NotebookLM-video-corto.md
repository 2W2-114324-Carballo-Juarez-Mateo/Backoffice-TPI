# NotebookLM — Video Overview (prompt corregido)

> **Para generar el video:** en NotebookLM, abrí el notebook con la fuente cargada (puede ser `Backoffice-Fuente-NotebookLM.md` o el `sdd-backend.zip`) y usá la función **"Video Overview"** (resumen en video) que genera NotebookLM desde las fuentes. Este prompt sirve para indicarle qué debe cubrir el video.

---

## Prompt (pegar en el chat o como instrucción del Video Overview)

Generá un **Video Overview (resumen en video)** de la fuente "Backoffice-Fuente-NotebookLM" para que nuestro equipo entienda y defienda la propuesta del **Backoffice (Tema 12)** frente al profesor.

El video debe explicar, con lenguaje claro y analogías simples:

> **Aclaración sobre el LLM (incluir en el video):** los **proveedores de LLM son empresas externas** (OpenAI, Anthropic, etc.). El **Backoffice NO es proveedor ni llama a los modelos**: solo **administra la configuración** (qué proveedores están habilitados y qué modelo usa cada función). Quien **utiliza** los modelos es el **Tema 07 (Evaluación LLM)**, que consume esa configuración.

1. **Qué es el Backoffice**: un **"consumidor puro"** (no fabrica datos, administra configuración y muestra/lee lo que producen otros temas) con **2 microservicios propietarios**: Administration & Configuration (parámetros PAR-01..24 + gestión del proveedor LLM exclusiva de ADMIN) y Reporting & Analytics (reportes docentes, panel del profesor, métricas CSAT 5★, exportación, alertas).
2. **Qué consume y qué lee**: consume identidad/auth/roles/2FA/auditoría (Tema 01) y cohorte/matrícula (Tema 02); lee datos de los Temas 02/04/05/07/08/10 (contratos de lectura). NO implementa identidad, cursos, desafíos ni economía.
3. **Requerimientos clave**: parámetros globales con cambios solo hacia adelante; proveedor LLM exclusivo de ADMIN con golden set + calibración + deriva; reportes por cohorte sin comparación entre docentes; frescura ≤15 min.
4. **Arquitectura e integración**: autorización en 2 niveles, toda llamada síncrona entre servicios pasa por el gateway, eventos por Kafka con Outbox + idempotencia, y cómo nos integramos con los otros 11 temas (qué damos y qué recibimos).
5. **Cierre**: por qué la propuesta es correcta (consumidor puro, contratos de lectura en sprint 1, proveedor exclusivo ADMIN).

Usá analogías para lo técnico (gateway = portero, Kafka = archivo central de avisos con offset/replay, consumidor puro = tablero de control) y hacé que el video sea didáctico, en español, de entre 3 y 6 minutos.