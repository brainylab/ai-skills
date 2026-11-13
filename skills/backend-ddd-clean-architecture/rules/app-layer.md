# Application layer — use cases and contracts

- Each use case represents one action of the system and exposes an `execute()` method with explicit input and output.
- It receives its dependencies through the constructor, using contracts instead of concrete implementations.
- Persistence contracts live in `application/repositories/`; contracts for other services the module needs live in `application/ports/`.
- It coordinates entities and dependencies, without knowing transport or database details.
- It returns application results and errors; HTTP status codes are defined in the presentation layer.
- Adopt a consistent strategy for results and errors, such as `Result`/`Either` or typed exceptions.
