# Contratos con T08 (Banco) — Negociación

> **De:** Equipo Backoffice (Tema 12) **↔** Equipo Banco (Tema 08)
> **Estado:** 🟡 **ACUERDO PARCIAL** — con la respuesta de Banco definimos nuestra postura final (abajo) y pedimos confirmación.

---

## 1. Respuesta de Banco (resumen)

**Aclaración de dirección:** la relación no es simétrica — en los contratos de lectura el Backoffice *lee* de Banco; Banco no consume nada de nosotros para eso.

- **Contract 1 · `bank.events`:** requiere ajuste. El **naming de eventos lo define T11** (convención de plataforma, no bilateral). `TransactionCreated`/`BalanceChanged` candidatos, sujeto a que `courseId`/`studentId` sean UUIDs de plataforma (T01/T02). `role` en el envelope pendiente de T01. **`MultiplierApplied` retirado** (rachas = "para más adelante" en T08 y depende de T10). Banco pide **justificar por qué el stream** y no polling al REST (nuestra frescura es ≤15 min, no tiempo real).
- **Contract 2 · REST `/api/bank/**`:** aceptado (balances, balance por alumno, transactions, acotados por `courseId`; acceso con token MS confirmado). Aclaran: `distribution` — si es **saldo en monedas** es Banco; si es **XP** es **T10**. **Rechazan** el agregado "retención vs desafíos" → esa integración (cruce T03/05 + Banco) es **responsabilidad del Backoffice**.
- **Contract 3 · Parámetros:** **NO APLICA.** Banco no calcula montos (solo registra): la derivación de XP/moneda (PAR-01/PAR-03) la ejecuta **T03 (Motor de Desafíos)** y emite el monto ya resuelto; **PAR-06/07** (precios de vida/equipamiento) son **catálogo de T09 (Mercado)**; **PAR-21 no existe en el PRD**. Sugieren redirigir a T03 (PAR-01/03) y T09 (PAR-06/07).
- **Contract 4 · Frescura/idempotencia:** `event_id` confirmado (si se implementa Contract 1); sin objeción al replay por REST. **`RF-RPT-06` no existe en el PRD** — el ≤15 min es decisión de arquitectura, no RF del PRD.

---

## 2. Nuestra postura / decisiones (acuerdo)

1. **REST-only para T08.** Los read models del Backoffice se sirven por **polling a `/api/bank/**`** (frescura ≤15 min alcanza; evitamos orden/duplicados/reconstrucción y la dependencia del naming de T11). **`bank.events` queda fuera del MVP**; si en el futuro se agrega, se coordina el naming con T11.
2. **`distribution`:** a Banco le pedimos **solo distribución de saldo en monedas**. La **distribución de XP** la pediremos a **T10 (Roadmap)**.
3. **"Retención vs desafíos":** lo **integra el Backoffice** (cruce de T03/05 + Banco) en `reporting-service`; **no** se lo pedimos a Banco.
4. **Contract 3 cerrado (no aplica):** actualizamos el ownership de parámetros → **PAR-01/03 a T03**, **PAR-06/07 a T09 (Mercado)**, **PAR-21 fuera** (no está en el PRD; queda como candidato suspendido, dependiente del mecanismo de rachas de T10).
5. **RF-RPT-06:** deja de citarse como RF del PRD → pasa a "decisión de arquitectura (frescura ≤15 min)".

---

## 3. Confirmación final que pedimos a Banco

1. ¿Confirman que los read models del Backoffice se sirven **solo por REST** (`/api/bank/**`, polling ≤15 min), sin `bank.events` en el MVP?
2. ¿Confirman que el endpoint `distribution` queda **solo para saldo en monedas** (el XP lo expone T10)?
3. ¿Confirmado que el agregado **"retención vs desafíos" lo integra Backoffice** (no lo expone Banco)?
4. Si más adelante se agregan eventos: ¿el **naming** de `bank.events` se define con **T11**?

---

## 4. Impacto en nuestros docs

- `PARAMETROS.md`: PAR-03 → **T03** · PAR-06/07 → **T09** · PAR-21 → **suspendido**.
- `CONTRATOS.md`: T08 → lectura REST (sin PAR) · se suman **T09** y **T11** como partners.
- Doc fuente: corrección de consumidores de `administration.events` (sin `bank`) y de "RF-RPT-06".
- US-08: los datos de T08 se ingieren por **REST**, no por Kafka.