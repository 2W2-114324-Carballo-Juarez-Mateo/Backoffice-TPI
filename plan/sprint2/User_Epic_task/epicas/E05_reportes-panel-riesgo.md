# [G06] — OBSERVABILIDAD, REPORTES Y PANEL DE RIESGO

> Épica #5 de Taiga · Sprint 2 (28/09 → 11/10/2026)

## Objetivo
Brindar a los docentes un panel claro para acompañar a sus alumnos y detectar a tiempo quiénes están en riesgo, y al administrador el tablero general de indicadores claves, protegiendo la privacidad y asegurando que no existan comparaciones ni rankings entre profesores, con reportes dinámicos y exportables.

## Suposiciones y Restricciones

### Suposiciones
- Cada docente accede únicamente a la información de los cursos y comisiones donde da clases.
- Los datos ya fueron consolidados por la épica de ingesta (EP-04).
- Los datos de actividad, entregas y encuestas se recolectan y actualizan continuamente a partir de lo que ocurre en la plataforma.
- La pertenencia docente y las encuestas las provee T02; si T02 no responde, el docente queda denegado y el ADMIN opera.

### Restricciones (legales/técnicas)
- Prohibición absoluta de rankings docentes: el sistema no compara profesores entre sí ni emite listados de rendimiento docente (RF-RPT-07).
- Protección del anonimato del estudiante: las encuestas solo muestran promedios con al menos 5 respuestas en el curso (PAR-18) y recién al cierre del curso (RF-ENC-13). Las abstenciones se informan aparte (RF-ENC-10).
- Privacidad entre colegas: un profesor no puede ingresar a las comisiones ni a las notas de otros profesores.
- RLS por curso: comisiones ajenas responden 403.
- Semáforo de riesgo con aviso automático (RF-RPT-03): cuando un alumno pasa a rojo se emite `STUDENT_AT_HIGH_RISK` una sola vez.
- Frescura máxima de 15 minutos (PAR-23), informada en cada reporte.

## Criterios de Aceptación a nivel Épico
- [ ] El conjunto mínimo de historias permite ver el panel docente, el tablero de indicadores y armar reportes dinámicos.
- [ ] Los indicadores principales se visualizan (participación, aprobación estimada, satisfacción con resguardo de anonimato y alertas tempranas de abandono).
- [ ] Queda garantizada la privacidad (anonimato) y la prohibición de comparaciones.
- [ ] Sin regresiones críticas en: la privacidad de los datos de alumnos y docentes; las restricciones de acceso por curso se cumplen sin excepción.
- [ ] Avisos automáticos cuando un alumno entra en riesgo y advertencias claras de datos desactualizados.

## Dependencias / Impactos
- Servicios / APIs: backoffice-service (módulo de reportes), T02 (pertenencia y encuestas).
- Módulos afectados: read models por cohorte, semáforo de riesgo, agregados, reportes dinámicos, exportación, alertas.
- Otros equipos: T02 (matrícula y encuestas), T03 (resultados), T05 y T10 (con flag), T11 (notificaciones).
- Impacto en datos / migraciones: V19 (read model), V20 (RLS), V21 (plantillas), V22 (encuestas), V23 (alertas, extra), V25 (exportación, extra).
- Feature toggles / flags: sí, T02 (pertenencia), T05 y T10 y el factor "vidas agotadas".

## Historias de usuario del Sprint 2

| Historia | Título | Acción en Taiga | Puntos | Prioridad | Tareas |
|---|---|---|---:|---|---:|
| [HU11](../historias/HU11_read-model-riesgo.md) (#25) | Read model y cálculo de riesgo por cohorte | Mover al Sprint 2 y actualizar la descripción | 5 | Must | 7 |
| [HU12](../historias/HU12_panel-docente.md) (#24) | Panel del docente con RLS y alerta de riesgo | Mover al Sprint 2 y actualizar la descripción | 5 | Must | 9 |
| [HU13](../historias/HU13_indicadores-csat.md) (#111) | Indicadores consolidados con bloqueo de anonimato | Mover al Sprint 2 y actualizar la descripción | 5 | Should | 8 |
| [HU14](../historias/HU14_umbrales-alertas.md) (#34) | Umbrales de aviso y acceso al tablero | Mover al Sprint 2 como extra y actualizar la descripción | 3 | Could | 6 |
| [HU09](../historias/HU09_exportacion-reportes.md) (#20) | Exportación de reportes | Sacar del sprint de G01 y mover al Sprint 2 como extra; actualizar la descripción | 5 | Could | 7 |
| [HU15](../historias/HU15_reportes-dinamicos-motor.md) (nueva) | Reportes docentes dinámicos (motor y ejecución) | CREAR historia nueva | 5 | Must | 7 |
| [HU16](../historias/HU16_constructor-reportes.md) (nueva) | Constructor de reportes (frontend) | CREAR historia nueva | 3 | Should | 4 |
