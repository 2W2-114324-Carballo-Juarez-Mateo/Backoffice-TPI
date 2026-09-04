# BackOffice — Tema 12 (Trabajo Integrador · 4° Cuatrimestre)

Plataforma de Aprendizaje Gamificado de Programación. Este repositorio contiene **toda la documentación fuente del módulo BackOffice** (Backend + Frontend + MSII), los entregables y el sitio desplegado.

## Estructura

| Ruta | Qué es |
|---|---|
| `backoffice_backend_requerimientos_arquitectura.md` | **Doc fuente del backend** (fuente de verdad): ADRs, RF/RNF, arquitectura, multitenancy/RLS, mensajería Kafka |
| `arquitectura_microservicios_plataforma_gamificada_v2.md` | Documento general de la plataforma (v2) |
| `BackOffice Resumen Visual.md` | Resumen visual del módulo |
| `frontend_plan_comunicacion.md` | Plan de comunicación del frontend (Caso A) |
| `frontend_arquitectura_analisis.md` | Investigación de arquitectura frontend (casos A/B) |
| `plan-seccion-interactiva.md` | Plan de la sección interactiva del sitio |
| `sdd/` | **SDD** (Software Design Documents): backend y frontend, con AGENTS.md para agentes de IA |
| `notebooklm/` | Fuente + prompts de NotebookLM (para generar videos de defensa) |
| `Presentacion-BackOffice.pptx` | Presentación generada (script en `docs-site/scripts/generate-pptx.mjs`) |
| `Presentacion-Gamma-BackOffice.md` | Markdown listo para generar la presentación en **Gamma** (10 slides) |
| `docs-site/` | **Sitio VitePress** (submódulo → `2W2-114324-Carballo-Juarez-Mateo/backoffice-docs`) |
| `*.pdf` | Documentos de la cátedra (PRD, propuesta BE, teoría FE) |

## Sitio desplegado

El sitio con toda la documentación (backend, frontend, MSII, sección interactiva con flujos animados):

> https://2W2-114324-Carballo-Juarez-Mateo.github.io/backoffice-docs/

## Clonar

```bash
git clone --recurse-submodules https://github.com/2W2-114324-Carballo-Juarez-Mateo/<REPO>.git
```

Si ya clonaste sin submódulos:

```bash
git submodule update --init --recursive
```

## Cómo se actualiza el sitio

El sitio se despliega automáticamente (GitHub Pages) al pushear al repo `backoffice-docs`. Los cambios de documentación se hacen acá (fuentes) y se reflejan en el sitio vía `docs-site/`.

## Stack (resumen)

Java 21 · Spring Boot 3 · PostgreSQL · **Kafka** (Outbox + idempotencia + caché TTL) · Eureka · Gateway de plataforma (T01) · Angular SSR + Nginx + BFF por experiencia · Multitenancy por curso-cohorte + RLS.