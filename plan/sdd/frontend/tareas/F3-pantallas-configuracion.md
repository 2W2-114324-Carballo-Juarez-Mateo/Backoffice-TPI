# Tarea F3 — Pantallas de configuración (Administration)

> **Sprint 2 · Talla M · ~3 persona-días** · consume Administration & Configuration vía BFF

## 1. Objetivo
Pantallas de **configuración global**: editar **PAR-01..24** (economía y operativos) y gestionar **proveedores LLM / evaluador / golden set** (exclusivas de ADMIN).

## 2. Alcance
- **In:** CRUD de parámetros (con versión actual), proveedores LLM, confirmación de cambios hacia adelante.
- **Out:** NO calcula economía; no guarda estado de negocio (el servidor es la fuente de verdad).

## 3. Requerimientos vinculados
RF-CFG-04 (economía global, solo ADMIN), RF-CFG-06 (hacia adelante), RF-IA-ADM (proveedor LLM exclusivo ADMIN).

## 4. Diseño técnico
- **Flujo:** pantalla → BFF `/config` → API Gateway → Administration service.
- **UI:** formularios con `@tup/ui`; **doble submit evitado** con single-flight + botones deshabilitados.
- **Idempotencia:** PUT de parámetro con `Idempotency-Key` (el backend la exige).
- **Feedback:** al guardar un PAR se muestra que rige **hacia adelante** (no recalcula históricos); el evento Kafka propaga a consumidores backend (el front solo ve el resultado).

## 5. Pruebas
CRUD PAR (solo ADMIN escribe; PROFESOR lee), edición de proveedor LLM, guardado con Idempotency-Key, mensajes de hacia adelante.

## 6. DoD
- [ ] Pantalla de PAR con versión actual y guardado idempotente.
- [ ] Gestión de proveedores LLM (solo ADMIN).
- [ ] Feedback de "cambios hacia adelante" correcto.
- [ ] Sin estado de negocio en el cliente.

> Reglas: `rules/RULES-stack.md` · `docs/03-comunicacion.md`.