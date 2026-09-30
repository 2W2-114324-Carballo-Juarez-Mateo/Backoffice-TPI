# [G06] — CONTRATOS DE LECTURA E INGESTA

> Épica #6 de Taiga · Sprint 2 (28/09 → 11/10/2026)

## Objetivo
Que el Backoffice consuma de forma confiable y deduplicada los datos de los demás temas y deje firmado, tema por tema, qué lee, por qué mecanismo y con qué payload.

## Suposiciones y Restricciones

### Suposiciones
- Los demás temas exponen sus lecturas por eventos o por REST.
- Sin contrato firmado se trabaja contra un puerto con flag y una alternativa documentada.

### Restricciones (legales/técnicas)
- El envelope de eventos es de 6 campos (`eventId`, `eventType`, `eventVersion`, `timestamp`, `producer`, `payload`).
- Los eventos duplicados o de versión menor se descartan y los malformados van a dead letter sin bloquear la partición.
- Un acuerdo por chat no cuenta hasta que queda en el `.md` del contrato.

## Criterios de Aceptación a nivel Épico
- [ ] Los contratos con T02, T03, T05, T07, T08, T10 y T11 tienen estado y fila de firma.
- [ ] Los consumidores de T03 y T02 funcionan; los de T05, T07 y T10 están detrás de flag.
- [ ] La frescura de cada fuente se calcula contra PAR-23 y se informa.

## Dependencias / Impactos
- Servicios / APIs: Kafka, REST de T02, T10 y T08.
- Módulos afectados: consumidores, deduplicación, read models de ingesta, registro de contratos.
- Otros equipos: T02, T03, T05, T07, T08, T10, T11.
- Impacto en datos / migraciones: V18 (topics del registro de contratos).
- Feature toggles / flags: sí, un flag por fuente sin contrato.

## Historias de usuario del Sprint 2

| Historia | Título | Acción en Taiga | Puntos | Prioridad | Tareas |
|---|---|---|---:|---|---:|
| [HU08](../historias/HU08_ingesta-temas.md) (#1628) | Ingesta de datos de los temas con deduplicación | Reabrir y mover al Sprint 2 (condicionada a contratos) | 5 | Should | 4 |
| [HU10](../historias/HU10_frescura-datos.md) (#28) | Control de frescura de los datos y avisos | Mover al Sprint 2 (en "In progress") y actualizar la descripción | 3 | Must | 3 |
| [HT01](../historias/HT01_contratos-entre-temas.md) (#1629) | Registro y gestión de contratos entre temas | Reabrir y mover al Sprint 2 | 5 | Must | 8 |
