# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 09 (Mercado)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Mercado (Tema 09)
> **Origen:** la negociación con T08 (Banco) y el profe dejaron claro que **PAR-03, PAR-06 y PAR-07** (monedas y precios de catálogo) **quedan fuera del Backoffice** y los gestiona **Mercado (T09)** o quien defina la cátedra/Hernán. Por eso **ya no les preguntamos por consumo de PAR-06/07** (los administran ustedes); solo confirmamos cómo se relacionan con el registro global.
> **Cómo usar este documento:** es una **solicitud**; respondan marcando opciones y completando tablas.

---

## 0. Contexto

El **Backoffice (Tema 12)** es dueño de los **parámetros globales (PAR)**. Según el reparto, **Tema 09 (Mercado)** arma la **oferta/catálogo** con esos precios y le indica a **Banco** la reserva/descuento a ejecutar. Por eso necesitamos confirmar el contrato de **consumo de parámetros** de Mercado.

## 1. Parámetros de economía que Mercado gestiona (fuera del Backoffice)

| PAR | Concepto | Valor de referencia | Estado |
|---|---|---|---|
| **PAR-03** | Monedas por desafío obligatorio/opcional | 100 / 50 | **EXTERNO** → T09/Mercado |
| **PAR-06** | Precio en monedas de 1 vida | 300 | **EXTERNO** → T09/Mercado |
| **PAR-07** | Precio en monedas de equipamiento | (default PRD) | **EXTERNO** → T09/Mercado |

**Confirmación que pedimos:**
1. ¿Mercado efectivamente **gestiona** PAR-03/06/07 (o los define la persona que mencionó Hernán)? ¿Dónde los mantienen (registro propio o siguen usando el `GlobalConfigurationChanged` del Backoffice)?

## 2. Lecturas de Mercado (opcional, para reporting)

RF-RPT-10 no incluye a Mercado entre los temas de lectura obligatorios, pero **si ustedes exponen catálogo/precios agregados** que enriquezcan métricas, lo aprovechamos. ¿Existe algo como:

| Endpoint (propuesta) | Método | Qué devuelve |
|---|---|---|
| `GET /api/market/catalog` | GET | Catálogo vigente (items, precios) |

Si prefieren no exponerlo, no nos bloquea.

## 3. Formato de respuesta

```markdown
## Contrato — <nombre>
- **¿Confirmado?** SÍ / NO / Requiere ajuste
- **PAR que consume:** ... | **Caché TTL:** ...
- **Notas:** ...
```

¡Gracias! Con esto cerramos el ownership de PAR-06/07 y dejamos documentado el flujo Mercado → Banco.