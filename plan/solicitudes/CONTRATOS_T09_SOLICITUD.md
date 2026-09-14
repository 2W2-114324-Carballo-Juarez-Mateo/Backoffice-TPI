# Solicitud de Contratos — Tema 12 (Backoffice) → Tema 09 (Mercado)

> **De:** Equipo Backoffice (Tema 12)
> **Para:** Equipo Mercado (Tema 09)
> **Origen:** la negociación con T08 (Banco) dejó claro que **PAR-06 y PAR-07** (precios de catálogo) los decide **Mercado**, no Banco. Necesitamos alinear ese contrato con ustedes.
> **Cómo usar este documento:** es una **solicitud**; respondan marcando opciones y completando tablas.

---

## 0. Contexto

El **Backoffice (Tema 12)** es dueño de los **parámetros globales (PAR)**. Según el reparto, **Tema 09 (Mercado)** arma la **oferta/catálogo** con esos precios y le indica a **Banco** la reserva/descuento a ejecutar. Por eso necesitamos confirmar el contrato de **consumo de parámetros** de Mercado.

## 1. Parámetros que Mercado consume del Backoffice

| PAR | Concepto | Valor de referencia |
|---|---|---|
| **PAR-06** | Precio en monedas de 1 vida | 300 |
| **PAR-07** | Precio en monedas de equipamiento | (default PRD) |

**Mecanismo:** publicamos `GlobalConfigurationChanged` (`{key, value, version}`) en `administration.events` (Transactional Outbox, idempotencia por `event_id` + versión).

**Confirmación que pedimos:**
1. ¿Mercado efectivamente **consume PAR-06 y PAR-07** para armar su catálogo? ¿Hay **algún otro PAR-01..23** que deba leer (ej. PAR-12 vidas, PAR-03 monedas)?
2. ¿Usan **caché local con TTL** (recomendamos 10 min) + invalidación por evento, como respaldo ante caída del Backoffice?

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