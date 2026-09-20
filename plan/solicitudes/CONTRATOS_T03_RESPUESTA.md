# Respuesta — Backoffice (T12) → Motor de Desafíos (T03)

> **En respuesta a:** su contestación de `CONTRATOS_T03_SOLICITUD.md`.
> **Estado:** acuerdo con ajustes — quedan 2 puntos a definir (PAR-20 y confirmación de Mercado sobre PAR-03), sin bloquear el MVP.

Aceptamos la propuesta con los siguientes ajustes:

## 1. Hecho único — ✅ Confirmado
`IntentoDesafioFinalizado` (con desglose y `parametros_aplicados` con versión) y `IntentoDesafioCerrado` en **`challenge.events`**. Consumiremos el evento para nuestros read models de engagement (frescura ≤15 min desde el cierre, como proponen).

## 2. Snapshot por intento — ✅ De acuerdo
Congelar **PAR-01/04/05 + `version`** al abrir el intento. Es lo que exige RF-CFG-06. En nuestro reporting segmentaremos por la versión del desglose si hace falta.

## 3. Endpoint REST — ✅ Confirmado
`GET /api/administration/parameters` devuelve todos los PAR con `{key, value, version}` (para poblar caché en arranque en frío).

## 4. PAR-03 (monedas) — bloqueante desbloqueado
**PAR-03 es de Mercado** (está entre los parámetros descartados del Backoffice). El Backoffice **no lo almacena**: para el monto de monedas del hecho único, **T03 debe acordar con Mercado que lo exponga** con el mismo mecanismo (evento + REST + versión). Lo coordinamos junto con ustedes/Mercado.

## 5. PAR-20 (ventana de gracia) — en suspenso
Lo dejamos en **suspenso** hasta definir qué hace exactamente una entrega dentro de la ventana (rechazar / aceptar con penalidad / solo marcar). Proponemos definirlo juntos y recién ahí confirmarlo como PAR-20. **No bloquea el MVP.**

## 6. Nuevo PAR "plazo máximo de corrección / vencimiento de intento" — ✅ Aceptado
Lo incorporamos como **candidato** del Backoffice (a validar con la cátedra) y lo sumamos al registro.

## 7. PAR-13 — ✅ Tomamos nota
Los cambios no son retroactivos (RF-CFG-06); el nuevo techo aplica a altas/ediciones posteriores.

## 8. Métricas — ✅ Aceptamos eventos
**`desafios.resultados`** (topic Drive oficial; antes `challenge.events`) para read models de engagement; usaremos `GET /api/challenges/courses/{courseId}` y `/progress` como complemento. Replay desde su outbox en **Sprint 2**: de acuerdo. **Envelope:** `EventoDTO` oficial (5 campos + headers).

Con esto, de nuestro lado el contrato con Motor de Desafíos queda cerrado (salvo PAR-20, a definir).