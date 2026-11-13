# Domain: checklist for entities and value objects

- The names of classes, properties, files and directories represent the implemented concept, not the illustrative names used in these rules.
- Imports point to the primitives and aliases available in the project.
- The `AggregateRoot` signature matches the shared implementation.
- The main identity is always a numeric `UniqueEntityID`, kept by the base class and preserved by the factory, with incrementing under the responsibility of the persistence layer.
- `PublicEntityID` represents only UUIDs in additional properties defined by the model, never the main identity or its generic parameter.
- `public_id` is not mandatory in every entity; when it exists, the factory and the mappers preserve its value without generating another UUID on reconstitution.
- Default values use `??` and do not overwrite valid explicit values.
- Getters expose the state that is needed, and setters exist only where a change is expected.
- Setters update the properties, the specific dates when they exist, and `updatedAt` through `touch()`.
- Value objects use `create()` or named factories according to the form of construction that is needed, distinguishing input normalization from reconstitution.
- The choice between entity, simple value and read projection follows the object's responsibility; IDs in the properties do not imply an identity of its own.
- Projections do not embed the ORM, queries or HTTP transport, and do not replace aggregates in change operations.
- The documented equality and mutability match the real implementation, including when the object does not inherit from `ValueObject`.
- Read collections do not receive change tracking merely because it exists in the corresponding aggregate.
- No methods, validations, errors, events, copy policies or equality strategies were added without an explicit requirement.
