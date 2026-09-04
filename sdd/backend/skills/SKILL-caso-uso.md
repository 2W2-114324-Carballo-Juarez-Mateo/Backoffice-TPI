# SKILL — Estructurar un caso de uso (Command + patrón)

## Pasos

1. **Identificá el comando:** verbo de negocio, ej. `RegisterModelProviderCommand`. Campos = lo que el caso de uso necesita (no DTOs crudos del HTTP).
2. **Capa application:** el handler recibe el comando, valida, invoca el dominio y persiste. No depende de Spring MVC.
3. **Capa domain:** la regla de negocio vive acá. Elegí el patrón según el caso:
   - Condición compuesta → **Specification**.
   - Política intercambiable → **Strategy**.
4. **Persistencia + eventos:** repositorio JPA + Outbox en la misma transacción (si el caso notifica).
5. **Auditoría:** si es una operación administrativa sensible, envolvé el comando con el **Decorator** de auditoría (`audit.application.AuditableCommandDecorator`).
6. **Tests:** unitario del handler (reglas) + integración (DB/broker).

## Anti-patrones a evitar

- Reglas de negocio en controllers.
- Entidades de dominio como DTOs de HTTP.
- Llamar a la DB de otro servicio.
- Implementar dominios de otros equipos (cursos, desafíos, usuarios).
- `if` dispersos para reglas que deberían ser Specification.

## Ejemplo de flujo

```text
Controller → Command → Handler (application)
                          ├── valida (specifications)
                          ├── Domain (regla)
                          ├── Repository (JPA)
                          └── Outbox (evento) + Audit (decorator)
```

> Ver `docs/06-patrones.md` para el mapa completo.