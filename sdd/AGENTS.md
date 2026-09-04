# AGENTS — Guía para agentes de IA

> Este archivo es el **punto de entrada** que las herramientas de IA (opencode, Claude, Copilot, Codex, Gemini) leen antes de trabajar. Su objetivo es **redirigir** al agente al documento correcto según la tarea, para que no lea información densa innecesaria (esto reduce alucinaciones).

## Proyecto

Plataforma de Aprendizaje Gamificado de Programación y Desarrollo de Software — módulo **BackOffice**. Trabajo integrador universitario (4to cuatrimestre). Documentación organizada por materias: **MSII** (análisis), **Back** (backend) y **Front** (frontend, sin plan definitivo aún).

## Dónde está cada cosa

| Si vas a trabajar en... | Entrá por acá |
|---|---|
| **Backend** (Java/Spring) | `sdd/backend/INDEX.md` |
| **Frontend** (Angular SSR + Nginx, Caso A) | `sdd/frontend/INDEX.md` |
| Requerimientos funcionales/no funcionales | documentos `*.md` de la raíz (fuente) + sitio `docs-site/` |

## Reglas globales (aplica siempre)

1. **No inventes**: si una decisión no está documentada, preguntá antes de asumir. No agregues funcionalidad que contradiga los planes.
2. **Mantené la documentación sincronizada**: si modificás un plan/implementación, actualizá el/los MD correspondiente(s) de `sdd/`. Es un requisito permanente: se modifican, agregan o reemplazan archivos según cambie el plan.
3. **Trazabilidad**: los requerimientos tienen IDs del PRD (`RF-ÁREA-NN`). Si tu cambio responde a uno, citá el ID.
4. **Backend no implementa frontend** y viceversa: respetá la división por materias.
5. Cuando termines una tarea, revisá que el `INDEX.md` de la sección correspondiente siga reflejando la estructura real de `docs/`, `rules/` y `skills/`.

## Estructura de cada sección

```
sdd/<backend|frontend>/
├── INDEX.md      ← mapa de redirección por tarea (LEELO PRIMERO)
├── docs/         ← diseño (SDD) dividido por tema
├── rules/        ← restricciones imperativas ("hacé/no hagas")
└── skills/       ← procedimientos concretos ("cómo se hace X")
```