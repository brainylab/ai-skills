# Domain: purpose, organization and naming

Define entities and value objects with a structure that is consistent and reusable across projects: typed properties, shared base classes, getters, setters when needed, and static factories.

The examples are structural models with illustrative names. Replace names, properties and default values with the business concept being implemented, without adding behaviors that were not requested.

Before applying the pattern, check the contracts of `Entity`, `AggregateRoot`, `ValueObject`, `DefaultProps`, `UniqueEntityID` and `Optional` available in the project, as well as `PublicEntityID` when there are public ID properties. The number of generic parameters and the construction signatures may vary, but the main identity of an entity is always `UniqueEntityID`. These rules do not require replacing those implementations.

## Organization and naming

- Place entities in `src/modules/<module>/domain/entities/`.
- Place value objects in `src/modules/<module>/domain/entities/value-objects/`.
- In existing projects, preserve the equivalent organization. For example, a project may use `src/domain/<context>/enterprise/entities/` and its `value-objects/` subdirectory; do not move those files just to reproduce the reference tree.
- Keep the shared primitives in `src/core/entities/` and the `Optional` type in `src/core/types/`.
- Use `kebab-case` files and `PascalCase` classes.
- Declare `<Name>Props` in the same file as the class. Export the interface when it is part of the contract consumed by other files.
- Preserve the property naming convention adopted by the project, without automatically converting between `camelCase` and `snake_case`.
- Adjust import aliases to the project. The examples in these rules use `@core/...` only as a reference.
- Use `import type` for dependencies used only as types. Classes instantiated in the file need a value import.
