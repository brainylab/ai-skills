# Module isolation

- Organize modules by business capability, such as `users`, `orders` and `billing`, not by database table.
- A module does not access another module's concrete repositories, schemas or internal details.
- When modules need to communicate, expose application contracts or events with explicit data.
- Sharing the same database does not remove the boundaries between modules.
- Use `core/` only for concepts that are truly shared. Do not move module-specific rules there to make imports easier.
- Contracts for integrations used by several modules may live in `gateways/`; contracts used by a single module stay in the consuming module.
