# NotebookLM — Video de RF y RNF (versión corta)

> **Antes:** tené cargada la fuente `Backoffice-Fuente-NotebookLM.md` (en sus secciones 3 y 4 están todos los RF y RNF).
> **Cómo:** usá **Video Overview** y pegá este prompt como instrucción.

---

## Prompt (corto)

Generá un **Video Overview** de la fuente "Backoffice-Fuente-NotebookLM" que recorra **todos los requerimientos funcionales (RF) y no funcionales (RNF)** que figuran en la fuente (sección 3 = RF, sección 4 = RNF), **uno por uno, sin omitir ninguno**, de forma didáctica en español y con **analogías simples** (gateway = portero, Kafka = buzón, consumidor puro = tablero de control, Database per Service = cuaderno propio, validar ≠ autorizar).

**Regla central:** nada aislado. Para cada requerimiento explicá: (1) qué pide, (2) por qué importa en el proyecto, (3) cómo encaja con el rol del Backoffice (consumidor puro: administra configuración y reporta; consume T01/T02; lee 02/04/05/07/08/10), (4) cómo se conecta con la arquitectura e integración (gateway, Kafka/Outbox, autorización, contratos de lectura, proveedor LLM → T07), y (5) un ejemplo o analogía. Usá frases puente tipo "esto se conecta con…", "porque somos consumidores puros…", "lo resolvemos con el gateway/Kafka porque…".

Organizá en 2 bloques: **RF** (configuración PAR, proveedor LLM, reportes/métricas/export/alertas) y **RNF** (seguridad, autorización, aislamiento, resiliencia, eventos, privacidad, observabilidad, etc.). Cerrá con un resumen de 20-30 segundos: Backoffice consumidor puro con 2 servicios propietarios que garantiza administración segura, consistente, trazable y reportable, integrada con los otros 11 temas.

Mantené fidelidad a la fuente; no inventes requisitos.