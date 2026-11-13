# Domain: structure and behavior of value objects

## Concept: entity versus value

An entity represents something whose continuity is tracked by an identity, even when its data changes. A value object represents a value or a composition of data; its meaning depends on its content, not on an identity of its own and an independent lifecycle.

- Use entities and aggregate roots for state and business behaviors that are tracked by identity.
- Use value objects to encapsulate concepts, normalizations, calculations or compositions of data that do not need an identity of their own.
- A value object may carry entity IDs as part of its properties. That does not turn it into an entity: those IDs reference the origin of the data, not an identity of its own managed by `Entity`.
- Value objects may also be used as read projections, as described in [domain-read-projections.md](domain-read-projections.md). This is an optional pattern, not an obligation to model every query result as a value object.

## Structure

- For new composite objects that follow the shared base class, extend `ValueObject<Props>`. Before changing an existing object, check whether it uses that base class or an independent implementation.
- Declare the properties in the same file as the class.
- Use the inherited protected constructor and expose data or results through getters.
- Build the instance through `static create(props)` or through named static factories.
- Do not receive a separate identity in the constructor of the value object base class.
- Properties may hold simple values, entities, other value objects and collections, as the contract requires.
- When the object includes dates, its contract may extend `DefaultProps` and expose those dates through getters. Do not impose that contract on every value object.

Do not add setters, `touch()` or domain events to value objects just to reproduce the structure of an entity. To represent another value or update a projection, prefer building a new instance.

Getters and the absence of setters do not guarantee deep immutability. In the reference base class, `protected props` restricts access in TypeScript, but it neither freezes nor copies the properties. Objects, dates and arrays received or returned by getters keep sharing references. Do not claim the object is immutable at runtime, and do not introduce defensive copies or freezing without an explicit requirement.

## Construction

- Use `create()` to receive the properties and return the instance.
- Distinguish creation from raw input from reconstitution of a value that is already stored. A normalization factory may transform the text; a reconstitution factory must preserve the value according to its contract.
- The presence of `create()` does not mean there is validation. Check the factory body before attributing validity guarantees to it.
- Use named factories when construction selects a context or an internal value.
- Separate `<Name>Params` from `<Name>Props` when the factory receives part of the data and adds properties before calling the constructor.
- Do not introduce validations, errors or conversions that are not part of the requested contract.

## Getters and calculations

- Use `value` when the object exposes one main result, such as a URL built from its properties.
- For composite objects, expose getters named after the corresponding data or results.
- Calculation methods may be used by getters to produce values derived from the properties.
- Receive the required data explicitly, such as a base URL, without depending on global configuration inside the object.
