# [G06] — CONFIGURACIÓN DE MODELOS LLM Y GOLDEN SET

> Épica #3 de Taiga · Sprint 2 (28/09 → 11/10/2026)

## Objetivo
Que el ADMIN gestione proveedores y modelos de IA desde el Backoffice, como fachada del servicio de evaluación (T07), y vea el veredicto de calibración y la deriva de los modelos.

## Suposiciones y Restricciones

### Suposiciones
- T07 es dueño de la evaluación: calcula el error, decide si el modelo queda aprobado y emite la deriva (Opción A).
- El Backoffice gobierna el parámetro PAR-14 (tolerancia) y muestra el resultado.
- La llamada a T07 pasa por el Gateway o por Eureka según lo que confirme T07 (tarea C1).

### Restricciones (legales/técnicas)
- Solo el ADMIN opera proveedores, modelos y calibración.
- La key de un proveedor viaja a T07 y nunca se loguea ni se devuelve.
- Un 401 o 403 de T07 nunca se traduce a 500.
- Si T07 no confirma el contrato el 02/10, la calibración queda con stub y flag.

## Criterios de Aceptación a nivel Épico
- [ ] El ADMIN registra proveedores y credenciales, lista y activa modelos contra T07 real.
- [ ] La activación rechaza un modelo no aprobado (409) y responde 503 si T07 no está disponible.
- [ ] El perfil y las corridas de calibración se consultan desde el Backoffice (si T07 confirmó).
- [ ] Cada operación tiene pruebas con WireMock y OpenAPI publicada.

## Dependencias / Impactos
- Servicios / APIs: backoffice-service, T07 (`/api/llm/admin/*`), API Gateway.
- Módulos afectados: clientes hacia T07, pantallas 09 y 10 y de calibración.
- Otros equipos: T07 (evaluación LLM), T01 (token de servicio).
- Impacto en datos / migraciones: ninguno (sin tablas propias de LLM).
- Feature toggles / flags: sí, stub de T07 con flag mientras no haya contrato; se retira al firmarlo.

## Historias de usuario del Sprint 2

| Historia | Título | Acción en Taiga | Puntos | Prioridad | Tareas |
|---|---|---|---:|---|---:|
| [HU04](../historias/HU04_proveedores-modelos-ia.md) (#18) | Registro de proveedores y modelos de IA | Reabrir y mover al Sprint 2 | 5 | Must | 6 |
| [HU05](../historias/HU05_conmutacion-modelos-ia.md) (#26) | Sustitución y conmutación de modelos de IA | Mover al Sprint 2 y actualizar la descripción | 5 | Must | 7 |
| [HU06](../historias/HU06_golden-set-calibracion.md) (#27) | Gestión del golden set y ejecución de revisión | Mover al Sprint 2 (condicionada a T07) y actualizar la descripción | 5 | Should | 9 |
| [HU07](../historias/HU07_tolerancia-par14-deriva.md) (#29) | Aprobación por tolerancia (PAR-14) y fallback por deriva | Mover al Sprint 2 (condicionada a T07) y actualizar la descripción | 5 | Should | 5 |
