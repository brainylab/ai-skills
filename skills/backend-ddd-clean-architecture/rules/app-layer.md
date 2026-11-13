# Application layer — use cases and contracts

- Each use case represents one action of the system and exposes an `execute()` method with explicit input and output.
- It receives its dependencies through the constructor, using contracts instead of concrete implementations.
- Persistence contracts live in `application/repositories/`; contracts for other services the module needs live in `application/ports/`.
- The repository contract of every aggregate root declares `nextId(): Promise<UniqueEntityID>`. A use case that creates an entity calls it before the entity's `create()` (see [domain-entity-identity.md](domain-entity-identity.md)).
- It coordinates entities and dependencies, without knowing transport or database details.
- It returns application results and errors; HTTP status codes are defined in the presentation layer.
- Adopt a consistent strategy for results and errors, such as `Result`/`Either` or typed exceptions.
