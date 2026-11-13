# Domain layer — business rules

- Entities represent concepts with identity; aggregate roots delimit groups of objects whose consistency is controlled together.
- Value objects represent values and their invariants, such as a valid email.
- Business rules live in the domain, not in controllers or database repositories.
- The domain knows nothing about HTTP requests, the ORM, environment variables or external SDKs.
- Add domain events and domain services when there is a need, without creating empty structures in advance.
