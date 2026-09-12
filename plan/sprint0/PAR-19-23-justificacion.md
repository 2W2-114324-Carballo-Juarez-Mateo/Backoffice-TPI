# PAR-19 a PAR-23 — Justificación para incorporarlos al proyecto

> Documento de apoyo para explicar a la cátedra por qué conviene sumar los parámetros `PAR-19..PAR-23` al catálogo del Backoffice (Tema 12). Están presentados como **candidatos deducidos de la especificación, a validar**. **PAR-24** (`session_inactivity_timeout_minutes`) fue **asignado al Tema 01** (ownership confirmado con el equipo de Usuarios en la negociación de contratos) y queda fuera del Backoffice.

## 1. De dónde sale la necesidad

- La **Lámina 6** de la propuesta de Backend asigna al Tema 12 el **"Registro de parámetros PAR-01 a PAR-24"**, y aclara que la economía la **aplica** el 03, el 05, el 08 y el 10 (deben leerla, no tenerla fija).
- La **tabla del PRD solo define hasta PAR-18** y esos 18 son los parámetros de **economía del juego**.
- El PRD (nota a **RF-CFG-04**) deja la puerta abierta: *"los parámetros **operativos** de plataforma (idioma, política de sesiones, retención) se completan en LL"*.
- Del rango PAR-19..24, **PAR-24 (sesión) quedó para el Tema 01**; el Backoffice conserva **PAR-01..PAR-23**.

## 2. Por qué conviene sumarlos (argumento en simple)

1. **Evita "números mágicos" en el código.** Hoy el 03/05/08/10 tendrían que poner valores fijos (ej. penalidad 30%, ventana 48 h, techo 3x). Si quedan hardcodeados, cualquier ajuste obliga a **recompilar y redesplegar** el microservicio. La Lámina 6 lo prohíbe.
2. **El ADMIN los gobierna desde una sola consola.** Subir un PAR en el Backoffice propaga el cambio (Kafka + Outbox) a quien lo consume, sin tocar código de otros equipos.
3. **Flexibilidad operativa.** Poder ajustar la penalidad de entrega tardía, el techo del multiplicador o el límite semanal de desafíos IA permite reaccionar a contingencias (paros, conectividad, presupuesto de IA) sin releases.
4. **Extensibilidad sin migraciones.** Nuestro registro de parámetros es **genérico** (clave + valor + versión): agregar PAR-19..23 no requiere cambiar el esquema ni migrar tablas.
5. **Cumple el espíritu del diseño.** Todo lo que no decide el profesor (ámbito pedagógico de su curso) ni el alumno, es **configuración global de ADMIN** (RF-CFG-05).

## 3. Los 5 candidatos deducidos

| PAR | Concepto | Valor de referencia | Cita en la especificación | Consume |
|---|---|---|---|---|
| **PAR-19** | Penalidad por entrega tardía | 30% | "Entrega tardía con penalidad del 30%…" | T05 |
| **PAR-20** | Ventana de gracia para entrega tardía | 48 h | "…en ventana de 48 h" | T03 / T05 |
| **PAR-21** | Techo del multiplicador de eventos/rachas | 3x | "Multiplicador de eventos con techo de 3x" | T08 / T10 |
| **PAR-22** | Límite semanal de desafíos personalizados IA | *propuesto* 5 | "Desafíos personalizados por LLM… Límite semanal de generación" | T03 / T07 |
| **PAR-23** | Frescura máxima de lectura en analítica | 15 min | "Frescura máxima de 15 minutos en los datos" | T12 (Reporting) |

> El valor "propuesto" de **PAR-22** **no tiene número oficial** en la documentación: se marcó así a propósito, esperando la validación de la cátedra. **PAR-24** (sesión) quedó asignado a **T01** (ownership resuelto en la negociación de contratos).

## 4. Lo que pedimos validar

1. Que sea correcto completar el catálogo hasta **PAR-23** con estos 5 **candidatos operativos** (o los que la cátedra indique).
2. Los **valores de referencia** (en especial PAR-22).

> Nuestro modelo de datos ya los soporta sin cambios (registro genérico extensible, base 18 del PRD + los que la cátedra confirme).