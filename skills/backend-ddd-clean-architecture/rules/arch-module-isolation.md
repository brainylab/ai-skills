# Module isolation

- Organize modules by business capability, such as `users`, `orders` and `billing`, not by database table.
- A module does not access another module's concrete repositories, schemas or internal details.
- When modules need to communicate, expose application contracts or events with explicit data.
- Sharing the same database does not remove the boundaries between modules.
- Use `core/` only for concepts that are truly shared. Do not move module-specific rules there to make imports easier.
- Contracts for integrations used by several modules may live in `gateways/`; contracts used by a single module stay in the consuming module.

## Public entry point: `index.ts`

A module that offers something to other modules exposes it in an `index.ts` at the module root. That file is the only path another module may import.

```ts
// modules/customers/index.ts
export { makeGetCustomerUseCase } from "./presentation/http/controllers/factories/make-get-customer-use-case";
export type { GetCustomerUseCaseResponse } from "./application/use-case/get-customer-use-case";
export { CustomerCreatedEvent } from "./domain/events/customer-created-event";
```

- Export only what is public on purpose: factories that return ready-to-use use cases or queries, their input and output types, and events. Never export entities, repositories, mappers or schemas.
- The consumer receives a use case that is already wired. It never builds another module's use case by hand, because that would require the other module's concrete repositories.
- Do not use `index.ts` inside its own module. Files of the module import each other directly; the entry point exists for the outside.
- A module that offers nothing to other modules has no `index.ts`. Do not create an empty one.
- `core/`, `gateways/` and `infra/` are not modules and are not subject to this rule.

## Who may import another module

Only a module's `infrastructure/` imports another module, and only through that module's `index.ts`.

```text
modules/billing/                                modules/customers/
  application/use-case/create-charge-use-case
        │ depends on
        ▼
  application/ports/customer-lookup             (owned by billing)
        ▲ implemented by
        │
  infrastructure/adapters/
    customers-module-customer-lookup  ───────►  index.ts
```

- `domain/`, `application/` and `presentation/` never import another module. A use case that needs something from another module declares a port in its own `application/ports/`.
- The port belongs to the consuming module. It describes what the consumer needs, in the consumer's terms, and returns a shape of its own, never the other module's entity.
- The adapter in `infrastructure/adapters/` implements the port, calls the other module through its `index.ts` and translates the result into the port's shape.
- When the other module moves to another process, only that adapter is replaced. The port and the use case stay the same.

```text
allowed:    billing/infrastructure/**  →  @/modules/customers
forbidden:  billing/domain/**, application/**, presentation/**  →  @/modules/customers
forbidden:  any file of billing  →  @/modules/customers/<anything>
```

## What an entity holds from another module

- An entity references something from another module only by its ID (`customerId: UniqueEntityID`), never by holding the other module's entity.
- Checking or fetching data to make a decision ("does the customer exist?", "is it active?") is done by the use case, through a port. The entity does not ask.
- When the entity needs the data as it was at that moment, the use case fetches it through the port and the entity stores a copy in a value object of its own module.

## Reacting to another module

- To react to something that happened in another module, subscribe to an event that module exposes in its `index.ts`. The subscriber lives in the reacting module's `infrastructure/`.
- The publishing module does not know who listens.

## Enforcement

Enforce these import rules with the project's linter, so that a violation fails the check instead of depending on review. Configure it when the second module is created.
