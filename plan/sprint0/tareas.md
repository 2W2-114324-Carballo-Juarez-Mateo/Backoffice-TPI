# Plan de Tareas — Backlog general (BackOffice · Tema 12)

> Todas las tareas de las 9 historias de usuario, agrupadas por tema y épica, listas para cargar en **Taiga**. Formato por tarea: **nombre** — qué hace (en simple). *(Talle · Horas)*. Los **responsables** se cargan después.

---

## TH-01 · Gobernanza y Configuración Institucional

### EP-01 · Parámetros Globales

#### US-01 · Modificación de parámetros globales (PAR-01..24)

1. **Crear el registro de parámetros** — La tabla y la estructura para guardar los parámetros (PAR-01..24) y poder buscarlos. *(M · 6 h)*
2. **Pantalla/API para ver y cambiar parámetros** — Los puntos para listar y modificar un parámetro, con sus validaciones. *(M · 8 h)*
3. **Guardar la versión y el historial** — Cada cambio queda anotado con su número de versión y quién/cuándo lo hizo. *(S · 4 h)*
4. **Regla de "el cambio vale de ahora en adelante"** — Impide que un cambio modifique resultados que ya pasaron. *(S · 4 h)*
5. **Guardar el aviso del cambio** — El cambio se registra junto con el aviso que luego se envía a los demás servicios. *(M · 8 h)*
6. **Pruebas de la lógica** — Tests que verifican versionado, permisos y validaciones. *(M · 6 h)*
7. **Pantalla en el front para editar** — La pantalla donde el administrador cambia el valor y ve el mensaje de que aplica de ahora en adelante. *(M · 8 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, actualizar el sdd y dejar la tarea lista en Taiga. *(S · 3 h)*

#### US-02 · Propagación del cambio de parámetro (Outbox + caché con TTL)

1. **Guardar el aviso pendiente** — La tabla donde queda registrado el aviso hasta que se envía. *(M · 8 h)*
2. **Enviar los avisos a Kafka** — El proceso que lee los avisos pendientes, los publica y los marca como enviados. *(M · 8 h)*
3. **Definir el formato del aviso** — El modelo del evento (clave, valor, versión) para que todos lo entiendan igual. *(S · 4 h)*
4. **Recibir el aviso en cada servicio** — El consumidor que guarda el valor en memoria y lo refresca por 10 minutos. *(M · 8 h)*
5. **Ignorar avisos repetidos** — Si llega un aviso que ya se aplicó o de una versión anterior, no vuelve a cambiar nada. *(S · 4 h)*
6. **Reintentar sin perder nada** — Si el envío falla, el aviso queda pendiente y se vuelve a intentar sin duplicar. *(M · 6 h)*
7. **Pruebas del envío** — Tests de integración: outbox, reintento y funcionamiento con el Backoffice caído. *(M · 8 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

### EP-02 · Administración de la Plataforma

#### US-03 · Asignación y revocación del rol administrador

1. **Crear el registro de administradores** — La tabla para guardar quiénes son administradores. *(M · 6 h)*
2. **Puntos para asignar, listar y quitar el rol** — Las operaciones para dar de alta, ver y dar de baja administradores. *(M · 8 h)*
3. **Proteger al último administrador** — Impide que un administrador se quite el rol a sí mismo y que el sistema quede sin administradores. *(S · 4 h)*
4. **Registrar todo en la auditoría** — Cada alta/baja queda anotada con su motivo. *(M · 6 h)*
5. **Avisar cuántos administradores quedan** — Al dar de baja se publica el aviso con el número restante. *(S · 4 h)*
6. **Pruebas de las reglas** — Tests de autorización, auto-revocación y protección del último admin. *(M · 6 h)*
7. **Pantalla de gestión de administradores** — La pantalla donde se asigna o quita el rol. *(M · 8 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

### EP-03 · Modelos LLM y Golden Set

#### US-04 · Registro y conmutación de proveedores de IA

1. **Crear el registro de proveedores y modelos** — Las tablas para guardar proveedores y modelos de IA. *(M · 6 h)*
2. **Puntos para alta, listado y activación** — Las operaciones para registrar un modelo, listarlos y activarlo. *(M · 8 h)*
3. **Manejar los estados del modelo** — Controla si un modelo está pendiente, aprobado, activo o retirado. *(S · 4 h)*
4. **Guardar y ocultar las claves** — Las claves se guardan cifradas y nunca se muestran completas. *(M · 6 h)*
5. **Avisar el cambio de modelo activo** — Al activar otro modelo, se avisa a los servicios que lo usan. *(S · 4 h)*
6. **Pruebas de la gestión** — Tests de alta, estados y protección de claves. *(M · 6 h)*
7. **Pantalla de gestión de proveedores** — La pantalla donde se registran y activan los modelos. *(M · 8 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-05 · Revisión de calidad de modelos de IA (golden set y tolerancia)

1. **Crear el registro de casos de referencia** — Las tablas para guardar las respuestas de referencia y los resultados de revisión. *(M · 6 h)*
2. **Proceso que prueba el modelo** — Ejecuta el modelo contra las respuestas de referencia y calcula el error promedio. *(L · 10 h)*
3. **Regla de aprobación por tolerancia** — Solo aprueba el modelo si el error está dentro de la tolerancia (PAR-14). *(S · 4 h)*
4. **Revisión periódica del modelo activo** — Controla cada cierto tiempo que el modelo en uso siga dentro de la tolerancia. *(M · 6 h)*
5. **Cambio automático a un respaldo** — Si el modelo se desvía, pasa a un respaldo y se avisa. *(M · 8 h)*
6. **Pruebas de la revisión** — Tests de aprobación, rechazo y deriva. *(M · 8 h)*
7. **Pantalla de resultados de revisión** — La pantalla donde se ven los resultados de cada revisión. *(M · 8 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

---

## TH-02 · Observabilidad y Soporte Académico

### EP-04 · Contratos de Lectura e Ingesta

#### US-06 · Recolección de datos de otros temas (ingesta y frescura)

1. **Crear el registro de datos recibidos** — Las tablas para guardar los datos que llegan y el estado de frescura. *(M · 6 h)*
2. **Recibir los datos de cada tema** — Los consumidores que escuchan los datos de los 6 temas. *(L · 12 h)*
3. **Evitar datos repetidos** — Si llega un dato ya procesado, no se vuelve a contar. *(S · 4 h)*
4. **Controlar que los datos estén al día** — Si un tema pasa 15 minutos sin enviar datos, se avisa y se marca. *(M · 6 h)*
5. **Separar datos con errores** — Los datos mal formados van a una cola aparte sin detener el resto. *(S · 4 h)*
6. **Pruebas de la ingesta** — Tests de deduplicación, frescura y descarte. *(M · 8 h)*
7. **Aviso visual de datos viejos** — El indicador en el panel que avisa cuando los datos están desactualizados. *(S · 4 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

### EP-05 · Observabilidad, Reportes y Panel de Riesgo

#### US-07 · Panel docente con alumnos en riesgo

1. **Armar el reporte por comisión** — El modelo de datos con el resumen de cada curso-cohorte. *(M · 8 h)*
2. **Calcular el estado de riesgo** — Define si cada alumno está en riesgo alto, medio o normal. *(M · 6 h)*
3. **Mostrar solo la comisión del docente** — El punto del panel que valida que el profesor solo vea sus comisiones. *(M · 8 h)*
4. **Avisar cuando un alumno está en riesgo** — Se envía el aviso al sistema de notificaciones. *(S · 4 h)*
5. **Pruebas del panel** — Tests de acceso por comisión y de cálculo de riesgo. *(M · 6 h)*
6. **Pantalla del panel docente** — La pantalla con el semáforo de riesgo de cada alumno. *(M · 8 h)*
7. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-08 · Tablero consolidado de indicadores (KPIs)

1. **Calcular los indicadores consolidados** — Los valores de satisfacción, aprobación, actividad, etc. desde los datos recibidos. *(M · 8 h)*
2. **Ocultar muestras muy chicas** — Si una comisión tiene pocas respuestas, su valor se oculta ("muestra insuficiente"). *(S · 4 h)*
3. **Umbrales de aviso configurables** — El administrador define cuándo un indicador está bajo y se avisa. *(M · 6 h)*
4. **Mostrar el tablero solo a ADMIN** — El punto del tablero que valida el permiso del administrador. *(M · 6 h)*
5. **Pruebas del tablero** — Tests de agregación, anonimato y permisos. *(M · 6 h)*
6. **Pantalla del tablero de indicadores** — La pantalla con las tarjetas de indicadores y sus metas. *(M · 8 h)*
7. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-09 · Exportación de reportes

1. **Pedir la exportación sin trabar la pantalla** — La solicitud responde al instante con un identificador y se procesa en segundo plano. *(M · 6 h)*
2. **Generar el archivo (PDF y CSV)** — Arma el reporte en segundo plano, en streaming para no quedarse sin memoria. *(L · 12 h)*
3. **Avisar cuando está listo** — Se avisa y queda disponible un enlace temporal de descarga. *(S · 4 h)*
4. **Respetar el alcance por rol** — El profesor solo exporta sus comisiones. *(S · 4 h)*
5. **Controlar el vencimiento del enlace** — Si el enlace venció, no se puede descargar. *(S · 4 h)*
6. **Pruebas de la exportación** — Tests de generación, alcance y vencimiento. *(M · 6 h)*
7. **Botón de exportación en el front** — El botón para pedir el reporte y el aviso de descarga. *(M · 6 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

---

> **Totales (referencia):** 9 historias · 69 tareas · horas estimadas a completar con la capacidad del equipo.