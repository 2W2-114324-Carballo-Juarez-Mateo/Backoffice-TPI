# Ajuste para Joaquín · Evento de parámetro: `key` → `paramKey`

> **Fecha:** 01/10/2026 · **Responsable:** Joaquín (autor de US-01 T4)
> **Motivo:** contrato definitivo de PAR-12 cerrado con Accounting (T11): el payload del evento `GLOBAL_CONFIGURATION_CHANGED` identifica el parámetro con **`paramKey`**. El publicador hoy emite `key` (estado *PROVISIONAL*).
> **No bloquea nada de T01** (Gateway): el cambio es independiente de la coordinación de identidad/scope.

## Qué hay que cambiar

Renombrar el campo **`key` → `paramKey`** en el payload del evento. El JSON serializado pasará de `"payload": { "key": ... }` a `"payload": { "paramKey": ... }`.

### 1 · `GlobalConfigurationChangedPayloadDto.java`

- **Archivo:** `src/main/java/ar/edu/utn/frc/tup/p4/administration/dtos/events/GlobalConfigurationChangedPayloadDto.java`
- Campo:
  - `private final String key;` → `private final String paramKey;`
- Actualizar el Javadoc del campo ("Parameter identifier in the registry, e.g. PAR-01").

### 2 · `ParameterChangeRecorderImpl.java`

- **Archivo:** `src/main/java/ar/edu/utn/frc/tup/p4/administration/services/impl/ParameterChangeRecorderImpl.java`
- En el builder del payload:
  - `.key(savedParameter.getParamKey())` → `.paramKey(savedParameter.getParamKey())`

### 3 · `ParameterChangeRecorderImplTest.java`

- **Archivo:** `src/test/java/ar/edu/utn/frc/tup/p4/administration/services/impl/ParameterChangeRecorderImplTest.java`
- En el assert del JSON del payload:
  - `payload.get("key")` → `payload.get("paramKey")` (la aserción que compara con `"PAR-13"`).

## Qué NO tocar

- **Los demás campos del payload** (`previousVersion`, `actorId`, `role`, `correlationId`) **se mantienen**: el Javadoc de la clase indica que actor y correlación viajan en el payload por acuerdo con T11. Solo cambia el nombre del campo del identificador.
- No tocar migraciones, ni el resto del registro de parámetros (eso es de Mateo).

## Por qué es importante

- El consumidor interno `ReferenceConfigConsumer` ya lee `payload.paramKey` — con `key` en el mensaje **no encontraba el campo** (desalineación latente). El rename lo corrige.
- Accounting va a consumir `paramKey` (contrato cerrado).

## Verificación

- `mvn -B clean verify` (pendiente del CI de la org; el billing está caído).
- Rama propia: `feature/tema-12-paramkey-event` (o la que prefiera Joaquín), PR a `develop`.