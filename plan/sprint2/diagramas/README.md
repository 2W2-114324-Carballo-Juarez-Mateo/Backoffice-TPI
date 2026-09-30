# Sprint 2 en diagramas — guion para explicarlo al profesor

> **Cómo verlos:** abrir `index.html` en el navegador. Cada diagrama es un HTML independiente (funciona sin internet): se puede enfocar un nodo, cambiar entre tema claro y oscuro y exportar a imagen. Las capturas `.png` sirven para las diapositivas. Los archivos `fuentes/*.json` permiten regenerarlos con Archify.
> **Duración sugerida:** 8 a 10 minutos. Un diagrama por bloque.

## Objetivo del sprint (30 segundos)

El Backoffice no tiene dominio propio: **lee** lo que producen otros temas y **administra** los parámetros compartidos (PAR-01 a PAR-24). En el Sprint 2 cerramos lo que quedó pendiente del Sprint 1 y construimos lo que más valor da al docente:

1. **Panel del profesor** con indicador de alumno en riesgo.
2. **Frescura máxima de 15 minutos** en los datos.
3. **Sin comparación entre docentes.**
4. **Reportes docentes dinámicos**, con KPIs de satisfacción de 5 estrellas y, como extras, exportación y alertas configurables.

## 1 · Arquitectura (`01-arquitectura-backoffice.html`)

- **Qué mostrar:** de izquierda a derecha, las fuentes (T02, T03, T05, T10, T08) alimentan a Kafka; el Backoffice consume, guarda en PostgreSQL y responde al frontend a través del Gateway.
- **Qué decir:**
  - Es el tema sin dominio propio: no puede mostrar nada hasta que los demás expongan sus lecturas. Por eso los contratos son la **ruta crítica**.
  - El Gateway (T01) **autentica**; el Backoffice **autoriza** por rol.
  - La gobernanza de IA (T07) es una **fachada solo para ADMIN**: el Backoffice no calcula nada de calibración.
  - Los eventos salientes (aviso de riesgo, exportación) salen por **outbox**, en la misma transacción.
- **Estado de los contratos** (las etiquetas de cada fuente): T03 firmado · T02 sin respuesta · T05 sin solicitud · T10 en curso · T08 acuerdo parcial.

## 2 · Flujo de datos (`02-flujo-datos.html`)

- **Qué mostrar:** tres carriles. El de arriba es el riesgo del alumno (T03), el del medio son las encuestas agregadas (T02) y el de abajo lo que espera contrato (T05 y T10).
- **Qué decir:**
  - Todo lo que llega se guarda **sin modificar**; lo mal formado va a *dead letter* sin frenar la partición.
  - Un proyector resume por curso **cada 5 minutos o menos**, así cada respuesta cumple la frescura de 15 minutos.
  - Las encuestas solo se muestran **agregadas**, con un mínimo de respuestas (PAR-18) y al cierre del curso. No guardamos quién respondió.
  - Lo que depende de un contrato sin firmar queda **detrás de un flag**.

## 3 · Secuencia de un reporte (`03-secuencia-reporte.html`)

- **Qué mostrar:** el recorrido de un pedido del profesor, de arriba hacia abajo.
- **Qué decir:**
  - Antes de consultar la base, un **resolvedor de alcance** confirma con T02 que ese profesor enseña ese curso.
  - Si el curso es ajeno responde **403** y no se toca la base de datos. Si T02 no responde, el profesor queda **denegado**.
  - Hay **doble protección**: la regla está en el código y además la base filtra por curso (*row level security*).
  - La respuesta informa **de cuándo son los datos**.
  - El reporte no acepta la dimensión "docente": así garantizamos que no hay comparación entre docentes.

## 4 · Regla de riesgo (`04-ciclo-riesgo.html`)

- **Qué mostrar:** el flujo de un alumno desde que entra al padrón hasta quedar en rojo, amarillo o verde.
- **Qué decir:**
  - **Rojo:** más de 10 días sin actividad o más de 60 % de reprobación. **Amarillo:** de 5 a 10 días o de 40 a 60 %. **Verde:** aprobación de 70 % o más.
  - La regla original de la historia dejaba **un hueco**: aprobación entre 60 y 70 % con poca inactividad. **Decidimos que va a amarillo** (preferimos avisar de más).
  - Con **menos de 3 intentos no se calculan porcentajes**: un alumno que falla su único intento no queda en rojo.
  - El aviso al docente sale **una sola vez**, al pasar a rojo.

## 5 · Plan por checkpoints (`05-plan-checkpoints.html`)

- **Qué mostrar:** los seis checkpoints del 29/09 al 11/10 y las cuatro franjas de trabajo.
- **Qué decir:**
  - **Primero cerramos el Sprint 1.** En Taiga cerramos 144 de 155 tareas (93 %), pero solo 6 de 13 historias: había trabajo terminado en ramas sin pull request.
  - **Después congelamos las interfaces compartidas** para que nadie se pise, y recién ahí construimos base de datos, riesgo, panel y reportes.
  - **El 02/10 hay un punto de control:** si T07 y T02 respondieron, sumamos calibración y encuestas; si no, queda un puerto con flag y seguimos. Orden de corte: extras, luego KPIs, luego el builder.

## Lo que aprendimos de la retrospectiva y cómo lo aplicamos

| Problema del Sprint 1 | Qué hacemos ahora |
|---|---|
| Tareas que se pisaban | Un dueño por paquete y un PR de contratos compartidos congelado |
| Planificar sin ajustar la carga | Reparto **parejo en líneas de código**: cerca de 2.900 por integrante en el núcleo |
| Taiga desactualizado | Corrección de estados en el CP0 y tarjeta de seguimiento por contrato |
| Comentarios de revisión por WhatsApp | Todo comentario queda en GitHub |
| Poca coordinación entre grupos | Contratos de lectura como historia prioritaria, con fecha tope el 02/10 |
| Frontend demorado | Las ramas ya terminadas se suben en el CP0 |

## Preguntas que puede hacer el profesor

- **¿Por qué no hay dominio propio?** Es parte de la propuesta de arquitectura: el Backoffice es un consumidor transversal y solo es dueño de los parámetros.
- **¿Qué pasa si otro equipo no entrega su contrato?** Se trabaja contra un puerto con flag y una alternativa documentada; lo que no se pueda mostrar se declara en pantalla.
- **¿Cómo se garantiza que un profesor no vea otro curso?** En dos capas: el servicio (403) y la base de datos (RLS por curso), con pruebas sobre PostgreSQL real.
- **¿Cómo se garantiza el anonimato de las encuestas?** Se reciben y guardan solo conteos agregados; no existe ningún dato que identifique a quien respondió.
