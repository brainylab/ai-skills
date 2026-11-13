# Domain: entity structure

- Declare the properties interface extending `DefaultProps`.
- Extend `AggregateRoot<Props>` or `AggregateRoot<Props, UniqueEntityID>`, according to the signature of the base class in use. The type of the main identity is always `UniqueEntityID`, never `PublicEntityID`.
- Use `this.props` to access the entity's protected state.
- Use the identity, the dates and the `touch()` method kept by the base class, without duplicating those members in the concrete entity.
- Do not declare a constructor just to repeat the inherited protected constructor.

The date contract used by the pattern is:

```ts
export interface DefaultProps {
  createdAt?: Date | null;
  updatedAt?: Date | null;
}
```

`Entity` holds identity, properties and dates. `AggregateRoot` extends that structure with support for domain events, when the shared implementation provides it. Creating an entity does not require adding events to the factory.
