# Plan — Sección interactiva del flujo (Back + Front)

> Objetivo: una sección del sitio (`backoffice-docs`) donde se vea **todo el flujo del BackOffice con animaciones**, tanto de backend como de frontend, para la defensa.

## 1. Idea central

Una **"visita guiada animada"** (`/interactivo/`) con escenarios clickeables que muestran el viaje de una operación de punta a punta: el usuario hace click en "siguiente paso" y **los datos se mueven animados** entre los componentes (Front → Nginx → BFF → Gateway → microservicio → DB/evento).

## 2. Escenarios propuestos

| # | Escenario | Qué animar | Muestra |
|---|---|---|---|
| E1 | **Login ADMIN** | Navegador → Nginx → BFF → Identity (cookie httpOnly) → rol ADMIN | Front: sesión, cookie, 2FA |
| E2 | **Cambio de PAR-01** | ADMIN → BFF → Gateway → Administration → `Outbox` → **Kafka** → consumidores + **caché TTL** (invalidación por evento, respaldo por TTL) | Back: híbrido REST+eventos, idempotencia por versión |
| E3 | **Reporte por curso (multitenancy)** | Front (selector curso) → BFF → Reporting → RLS filtra filas | Back: TenantContext, `app.current_course`, RLS |
| E4 | **Prueba de aislamiento 200/403** | PROFESOR A → curso A = 200 · curso B = 403 · intento ALL = 403 · ADMIN → ALL = 200 | Back: multitenancy + caso ADMIN global |
| E5 | **Despliegue** | Git push → CI → Docker 2 etapas → imagen → compose → Nginx (envsubst) → Rolling Update | Front: build, reverse proxy, estrategias |

## 3. Cómo (tecnología)

El sitio es **VitePress (Vue 3)** → se puede hacer con **componentes Vue propios** + SVG/CSS/JS, sin librerías pesadas:

- **Mermaid** (ya integrado) para los diagramas base estáticos.
- **Componente Vue `FlowPlayer`**: escenario = lista de pasos `{componente, acción, mensaje}`; anima **nodos y flechas SVG** (movimiento de "paquetes" con `<animateMotion>` o JS + `requestAnimationFrame`).
- **Controles**: play/paso a paso, velocidad, reiniciar; tooltips en cada componente.
- **Estilo**: colores por rol (Front=naranja, Back=azul, T01/plataforma=gris, DB=verde), flechas animadas con CSS keyframes.
- Sin dependencias nuevas si usamos SVG/CSS nativos; opcional **d3** si queremos diagramas dinámicos.

## 4. Estructura

```
docs/interactivo/
├── index.md                  ← portada + navegación de escenarios
├── e1-login.md
├── e2-cambio-par.md
├── e3-reporte-curso.md
├── e4-aislamiento-403.md
└── e5-despliegue.md
docs/.vitepress/theme/components/
├── FlowPlayer.vue            ← motor de animación por pasos
├── FlowNode.vue              ← componente/nodo del diagrama
└── scenarios/*.ts            ← datos de los 5 escenarios
```

## 5. Fases

| Fase | Contenido | Esfuerzo |
|---|---|---|
| **1** | Motor `FlowPlayer` + escenario E1 (login) + portada | ~2 días |
| **2** | E2 (Kafka + caché TTL) y E3 (RLS) — los más importantes para la defensa | ~2 días |
| **3** | E4 (200/403 + ADMIN ALL) y E5 (despliegue) | ~2 días |
| **4** | Pulido (animaciones, tooltips, responsive, deploy) | ~1 día |

## 6. Decisiones a confirmar

1. ¿**Solo BackOffice** (nuestro flujo) o también el flujo de otros temas que consumimos?
2. ¿Nivel de interactividad: **paso a paso guiado** (recomendado) vs **animación automática continua**?
3. ¿Esperar el **plan definitivo del front** para E1/E5, o arrancar con los escenarios de back (E2-E4) que ya están consolidados?

> Propuesta: arrancar por **E2 + E3 + E4** (back consolidado) y dejar E1/E5 para cuando el front se implemente.