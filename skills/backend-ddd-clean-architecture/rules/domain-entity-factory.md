# Domain: static `create()` factory

Follow this sequence:

1. Receive the typed properties.
2. Use `Optional<Props, Fields>` to indicate the fields that may be omitted on creation.
3. Receive the main `id` as a `UniqueEntityID` in the second argument; additional public ID properties, when the model has them, belong to `props`.
4. Instantiate the class itself, preserving the properties with `...props`.
5. Apply the required default values with `??`.
6. Forward the identity to the constructor.
7. Return the created instance.

`Optional<T, K>` makes the selected properties of `T` optional. It may include dates according to the adopted signature convention, even when they are already optional in `DefaultProps`.

Use `??` to preserve values such as `false` and `0`. Do not add validations, normalizations or new error types just to fill in the factory.

## Dates on construction

- Preserve dates received in the properties.
- When the factory sets `createdAt`, use `props.createdAt ?? new Date()`.
- When dates are initialized by the base class, respect that contract.
- Business-specific dates may stay optional until a setter updates them.
- Assigning values to `props` on construction does not run the entity's setters.
