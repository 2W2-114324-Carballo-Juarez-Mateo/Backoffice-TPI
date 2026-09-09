# Plan de Tareas — Backlog general (BackOffice · Tema 12)

> Todas las tareas de las 14 historias de usuario, agrupadas por tema y épica, listas para cargar en **Taiga**. Formato por tarea: **nombre** — qué hace (en simple). *(Talle · Horas)*. Los **responsables** se cargan después.

---

## TH-01 · Gobernanza y Configuración Institucional

### EP-01 · Parámetros Globales

#### US-01 · Modificación y versionado de parámetros globales

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

#### US-04 · Registro de proveedores y modelos de IA

1. **Crear las entidades de proveedor y modelo** — Las tablas para guardar proveedores y modelos de IA. *(M · 6 h)*
2. **Alta de proveedor** — El punto para registrar un proveedor con sus parámetros. *(M · 6 h)*
3. **Listado de modelos con estado** — El punto para ver el catálogo y el estado de cada modelo. *(S · 4 h)*
4. **Guardar y ocultar las claves** — Las claves se guardan cifradas y nunca se muestran completas. *(M · 6 h)*
5. **Estado inicial "pendiente de revisión"** — Todo modelo nuevo queda en espera y no se puede activar. *(S · 4 h)*
6. **Pruebas de la gestión** — Tests de alta, listado y protección de claves. *(M · 6 h)*
7. **Pantalla de registro** — La pantalla para cargar proveedores y modelos. *(M · 8 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-05 · Sustitución y conmutación de modelos de IA

1. **Activación de modelo** — El punto para activar un modelo como el que se usa. *(S · 4 h)*
2. **Validar que esté aprobado** — Impide activar un modelo que no pasó la revisión (responde 409). *(S · 4 h)*
3. **Un solo modelo activo** — Garantiza que haya un único activo por función. *(S · 4 h)*
4. **Publicar el aviso de cambio** — Al conmutar se publica `ModelProviderChanged` con el anterior y el nuevo. *(M · 6 h)*
5. **Actualizar estados** — El activo pasa a reserva y el nuevo a activo. *(S · 4 h)*
6. **Pruebas de conmutación** — Tests de activación, rechazo y evento. *(M · 6 h)*
7. **Acción de activar en el front** — El botón para activar un modelo. *(S · 4 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-06 · Gestión del golden set y ejecución de revisión

1. **Crear el registro de casos de referencia** — Las tablas para guardar las respuestas de referencia y los resultados. *(M · 6 h)*
2. **Proceso que prueba el modelo** — Ejecuta el modelo contra los casos y calcula el error promedio. *(L · 10 h)*
3. **Ejecutar la revisión** — El punto para lanzar una revisión. *(S · 4 h)*
4. **Consultar los resultados** — El punto para ver el detalle de cada revisión. *(S · 4 h)*
5. **Pruebas de la revisión** — Tests de cálculo y de casos sin referencia. *(M · 6 h)*
6. **Pantalla de resultados** — La pantalla con los resultados de cada revisión. *(M · 6 h)*
7. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-07 · Aprobación por tolerancia (PAR-14) y fallback por deriva

1. **Regla de aprobación por tolerancia** — Aprobado si el error está dentro de PAR-14; rechazado si no. *(S · 4 h)*
2. **Bloquear modelos rechazados** — No se puede activar un modelo rechazado. *(S · 4 h)*
3. **Revisión periódica del activo** — Controla cada cierto tiempo que el modelo en uso siga dentro de la tolerancia. *(M · 6 h)*
4. **Cambio automático al respaldo** — Si se desvía, pasa a un respaldo. *(M · 6 h)*
5. **Alerta de deriva** — Se emite la alerta crítica cuando el modelo se desvía. *(S · 4 h)*
6. **Pruebas de aprobación/deriva** — Tests de aprobación, rechazo y deriva. *(M · 6 h)*
7. **Estado del modelo en el front** — La pantalla muestra el estado y el aviso de deriva. *(S · 4 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

---

## TH-02 · Observabilidad y Soporte Académico

### EP-04 · Contratos de Lectura e Ingesta

#### US-08 · Ingesta de datos de los temas con deduplicación

1. **Tabla de datos procesados** — La tabla que guarda los identificadores de datos ya recibidos. *(M · 6 h)*
2. **Consumidores por tema** — Los receptores de datos de cada uno de los 6 temas. *(L · 12 h)*
3. **Deduplicación por identificador** — Si llega un dato ya procesado, no se vuelve a contar. *(S · 4 h)*
4. **Cola de datos mal formados** — Los datos con errores van a una cola aparte sin detener el resto. *(S · 4 h)*
5. **Pruebas de la ingesta** — Tests de deduplicación y descarte. *(M · 6 h)*
6. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-10 · Control de frescura de los datos y avisos

1. **Monitor de frescura** — El control periódico del estado de cada tema. *(M · 6 h)*
2. **Calcular el desfase** — Compara el último dato recibido con el umbral de 15 minutos. *(S · 4 h)*
3. **Avisar y marcar** — Emite el aviso y marca los reportes afectados. *(S · 4 h)*
4. **Quitar el aviso al normalizar** — Cuando el tema vuelve a enviar datos, se retira la marca. *(S · 4 h)*
5. **Estado de frescura por API** — El punto que expone si los datos están al día. *(S · 4 h)*
6. **Insignia en el front** — El indicador visual de datos desactualizados. *(S · 4 h)*
7. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

### EP-05 · Observabilidad, Reportes y Panel de Riesgo

#### US-09 · Exportación de reportes

1. **Solicitud de exportación asíncrona** — Responde al instante con un identificador y se procesa en segundo plano. *(M · 6 h)*
2. **Generar el archivo (PDF y CSV)** — Arma el reporte en segundo plano, en streaming. *(L · 12 h)*
3. **Aviso de archivo listo** — Se avisa y queda disponible un enlace temporal. *(S · 4 h)*
4. **Alcance por rol** — El profesor solo exporta sus comisiones. *(S · 4 h)*
5. **Vencimiento del enlace** — Si el enlace venció, no se puede descargar. *(S · 4 h)*
6. **Pruebas de la exportación** — Tests de generación, alcance y vencimiento. *(M · 6 h)*
7. **Botón de exportación en el front** — El botón para pedir el reporte y el aviso de descarga. *(M · 6 h)*
8. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-11 · Read model y cálculo de riesgo por cohorte

1. **Read model por comisión** — El modelo de datos con el resumen de cada curso-cohorte. *(M · 8 h)*
2. **Cálculo del estado de riesgo** — Define si cada alumno está en riesgo alto, medio o normal. *(M · 6 h)*
3. **Procesamiento periódico** — La foto analítica que recalcula el riesgo. *(M · 6 h)*
4. **Pruebas del cálculo** — Tests de las reglas de riesgo. *(M · 6 h)*
5. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-12 · Panel del docente con RLS y alerta de riesgo

1. **Endpoint del panel docente** — El punto que devuelve el panel de una comisión. *(M · 8 h)*
2. **Validar acceso por comisión (RLS)** — Solo el profesor de esa comisión accede; otras responden 403. *(M · 6 h)*
3. **Aviso de riesgo alto** — Se envía el aviso cuando un alumno pasa a riesgo alto. *(S · 4 h)*
4. **Sin comparaciones entre docentes** — La vista no permite comparar a los docentes. *(S · 4 h)*
5. **Pruebas de acceso y aviso** — Tests de RLS y de aviso. *(M · 6 h)*
6. **Pantalla del panel docente** — La pantalla con el semáforo de riesgo. *(M · 8 h)*
7. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-13 · Indicadores consolidados con bloqueo de anonimato

1. **Calcular los indicadores consolidados** — Los valores de satisfacción, aprobación, actividad, etc. desde los datos recibidos. *(M · 8 h)*
2. **Bloqueo de anonimato** — Si una comisión tiene pocas respuestas, su valor se oculta. *(S · 4 h)*
3. **Endpoint del tablero (solo ADMIN)** — El punto del tablero con permiso de administrador. *(M · 6 h)*
4. **Pruebas de agregación y anonimato** — Tests de agregados, anonimato y permisos. *(M · 6 h)*
5. **Pantalla del tablero** — La pantalla con las tarjetas de indicadores y sus metas. *(M · 8 h)*
6. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

#### US-14 · Umbrales de aviso y acceso al tablero

1. **Configurar umbrales** — El ADMIN define cuándo un indicador está bajo lo esperado. *(S · 4 h)*
2. **Evaluar y emitir el aviso** — Si un indicador baja del umbral, se avisa. *(S · 4 h)*
3. **Restringir el acceso** — Solo ADMIN ve el tablero y los avisos. *(S · 4 h)*
4. **Pruebas de umbral y permisos** — Tests de umbral, aviso y acceso. *(M · 6 h)*
5. **Configuración de umbrales en el front** — La pantalla para fijar los umbrales. *(S · 4 h)*
6. **Documentar y cargar en Taiga** — OpenAPI, sdd y tarea en Taiga. *(S · 3 h)*

---

> **Totales (referencia):** 14 historias · 100 tareas · horas estimadas a completar con la capacidad del equipo.