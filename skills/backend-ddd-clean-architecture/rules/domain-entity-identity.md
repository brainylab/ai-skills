# Domain: identity and base class signature

## Main identity: always `UniqueEntityID`

- The main `id` of every entity is always `UniqueEntityID`, wrapping a `number` associated with the auto-increment identifier of the persistence layer. Do not accept UUIDs or strings in this primitive.
- Keep the `id` in the base class, outside the properties interface. Receive it as the second argument of `create()` and forward it to the constructor without replacing it.
- Incrementing is the responsibility of the persistence layer, not of the entity or the identity wrapper. Do not use `new UniqueEntityID()` without a value, and do not generate random or sequential numbers in the domain.
- If the ID only exists after insertion, follow the project's contract for entities not yet persisted, without changing its type to `PublicEntityID` or inventing provisional IDs.

## Additional public ID property: `PublicEntityID`

- Use `PublicEntityID`, which is exclusively a UUID, only when the model has an additional public ID property, such as `public_id`. That property neither replaces nor changes the main `id`.
- Declare the property in `<Name>Props` and expose it through a getter, like the rest of the entity's data. Preserve the project's naming convention.
- The existence of the `PublicEntityID` type does not require adding `public_id` to every entity. When the model requires the field, it may be mandatory in the properties; what is optional is its existence in the model, not necessarily its value.
- Do not use `PublicEntityID` as the identity argument of the base class or as the generic type of the main identity.
- Preserve UUIDs that are received. Generate a new UUID only when creation calls for it, following the primitive's API; never when reconstituting an existing entity.
- Mappers must preserve the main `id` and the public property, when present, separately. Using that property in external contracts depends on the project's requirements and does not replace authorization.

## Generic parameters

The two forms below are alternatives, not interchangeable signatures:

- `AggregateRoot<ConfigurationProps>`: for a base class that receives only the properties contract.
- `AggregateRoot<ConfigurationProps, UniqueEntityID>`: for a base class that also receives the identity type.

Choose the form the project supports. Do not add a generic parameter to a base class that does not declare it.
