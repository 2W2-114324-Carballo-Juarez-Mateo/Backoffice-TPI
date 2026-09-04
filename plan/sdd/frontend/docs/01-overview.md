# 01 — Overview del Frontend (Caso A)

## Postura

**Caso A**: apps Angular SSR independientes en **multirepos**, presentadas por **Nginx** bajo un mismo dominio (`plataforma.edu.ar`). Cada dominio (Alumno, Profesor, BackOffice) es una app propia. SSR entrega el HTML inicial y Angular hidrata luego.

## Decisiones base

| Decisión | Elección |
|---|---|
| Integración | Caso A — multirepos + Nginx |
| BFF | **Uno por experiencia** (BackOffice, Alumno, Profesor) |
| Compartir estado | Custom Events + Storage (sin store global cross-app) |
| Sesión | Cookie httpOnly en dominio compartido (propiedad de Identity) |
| UI compartida | Librería `@tup/ui` (tokens + componentes) |
| Entrada pública | Nginx (reverse proxy + enrutador técnico) |

## Estado

- **Frontend con plan definido:** alineado a la consigna de Front (Arquitectura y Despliegue U1). Para el BackOffice: Nginx (web + reverse proxy + deep-links), **BFF por experiencia** (del equipo BackOffice), build Docker 2 etapas con `envsubst`, y despliegue **Rolling Update + Feature Flags**. Detalle: `docs/09-despliegue.md`.

## Por qué Caso A

- **Autonomía entre equipos** (cada grupo avanza a su ritmo, sin coordinar releases con un Shell).
- **Aislamiento de fallas** (un bug en una app no tumba las demás).
- **Cero conflictos de repos** (multirepos, sin merges compartidos).
- **Independencia de versionado/deploy** y **SSR** (mejor primer paint).

**Costos reconocidos:** consistencia visual (→ `@tup/ui`), duplicación de bootstrap, coordinación de sesión (→ contrato de cookie).

> Fuente: `frontend_arquitectura_analisis.md` · `frontend_plan_comunicacion.md`.