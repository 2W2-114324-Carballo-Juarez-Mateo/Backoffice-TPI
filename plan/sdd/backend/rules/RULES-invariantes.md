# RULES — Invariantes de negocio (NUNCA violar)

Estas reglas vienen del PRD y **no se negocian en código**. Si un requerimiento parece contradecirlas, preguntá antes de cambiar nada.

1. **Último ADMIN:** el sistema nunca puede quedar con cero ADMIN activos. Bloqueo incondicional; validar con **transacción + revalidación** (no solo pre-validación).
2. **Auto-eliminación:** un ADMIN no puede eliminarse a sí mismo (RF-ROL-02).
3. **Baja de ADMIN reforzada:** requiere contraseña + 2FA + confirmación escrita (RF-ROL-06).
4. **ADMIN sin sub-niveles:** todos los ADMIN tienen los mismos permisos (RF-ROL-01).
5. **Sin hard delete:** toda la producción académica usa **baja lógica** (`deleted_at`/`deleted_by`/`deletion_reason`).
6. **Configuración hacia adelante:** un cambio de parámetro global **nunca** recalcula XP/monedas históricos (RF-CFG-06).
7. **Ámbito de configuración:** PROFESOR no puede modificar parámetros globales (PAR-01..18), solo ADMIN (RF-CFG-05).
8. **Proveedores de modelo exclusivos de ADMIN:** la gestión de proveedores/modelos de LLM es potestad exclusiva del ADMIN y queda auditada (RF-IA-35).
9. **Evaluador único + calibración:** el evaluador de uso de IA tiene un único modelo activo; cambiar el evaluador exige calibración dentro de tolerancia (PAR-14) — sin override (RF-IA-25/28/31).
10. **Auditoría inmutable:** los eventos de auditoría no se modifican desde las APIs administrativas (RF-AUD-04).
11. **Anonimato de encuestas:** en reportes/métricas se consumen solo **agregados anónimos**; ninguna operación reconstruye autor ↔ respuesta (RF-ENC-04/12).
12. **No acceder a la DB de otro servicio:** Database per Service; relaciones solo por API/eventos.
13. **No implementar dominios de otros equipos:** el BackOffice **no** crea servicios/entidades de cursos, desafíos ni usuarios (solo consume eventos).

> Si necesitás "bajar" una de estas reglas, es decisión del Product Owner, no del código.