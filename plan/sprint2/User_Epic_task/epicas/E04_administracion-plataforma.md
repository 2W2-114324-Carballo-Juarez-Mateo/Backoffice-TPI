# [G06] — ADMINISTRACIÓN DE LA PLATAFORMA

> Épica #4 de Taiga · Sprint 2 (28/09 → 11/10/2026)

## Objetivo
Que el ADMIN gestione quién administra la plataforma y que cada pantalla del Backoffice muestre solo lo que el rol permite.

## Suposiciones y Restricciones

### Suposiciones
- La gestión de cuentas e identidad es de T01; el Backoffice la consume y la audita.
- El rol llega al Backoffice por los headers de confianza del Gateway.

### Restricciones (legales/técnicas)
- Un ADMIN no puede auto-eliminarse y toda baja exige confirmación (RF-ROL-02 y RF-ROL-06).
- La auditoría de acciones administrativas se publica en `identity.audit.events`.
- Las rutas del frontend solo para ADMIN están protegidas por guards.

## Criterios de Aceptación a nivel Épico
- [ ] El ADMIN asigna y revoca el rol administrador con auditoría.
- [ ] GESTOR y PROFESSOR no acceden por URL directa a pantallas solo para ADMIN.
- [ ] El topic de auditoría se configura por propiedad y no por constante.

## Dependencias / Impactos
- Servicios / APIs: backoffice-service, API Gateway (T01).
- Módulos afectados: guards de rutas, auditoría administrativa.
- Otros equipos: T01 (cuentas, 2FA y auditoría).
- Impacto en datos / migraciones: ninguno.
- Feature toggles / flags: no.

## Historias de usuario del Sprint 2

| Historia | Título | Acción en Taiga | Puntos | Prioridad | Tareas |
|---|---|---|---:|---|---:|
| [HU03](../historias/HU03_rol-administrador.md) (#182) | Asignación y revocación del rol administrador | Mover al Sprint 2 (en "In progress") | 5 | Must | 2 |
