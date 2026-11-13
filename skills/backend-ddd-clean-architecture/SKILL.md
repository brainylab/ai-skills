---
name: backend-ddd-clean-architecture
description: Create, review or refactor backends organized as a Modular Monolith with DDD and Clean Architecture. Use when structuring business modules, implementing entities, value objects, use cases, contracts, repositories, gateways, adapters, controllers and tests, or when auditing dependencies between layers and isolation between modules. Consult the rules in `rules/` according to the task and adapt the instructions to the project's stack and conventions.
---

# Backend: Modular Monolith with Clean Architecture

## Scope

Apply this skill when implementing features, reviewing boundaries or refactoring backends that follow this architecture. Do not impose an architectural migration when fixing a small task in a project organized differently.

## Rules on demand

The details and examples live in the files next to this skill. Resolve the paths below from the folder that contains this `SKILL.md`, not from the root of the target project. Consult only the files and sections relevant to the task.

### Rules (`rules/`)

Each file covers a single topic and can be read on its own. The file name prefix indicates the area:

| Prefix | Area |
| --- | --- |
| `arch-` | General architecture: structure, dependencies, modules and conventions. |
| `domain-` | Domain layer. |
| `app-` | Application layer. |
| `infra-` | Infrastructure layer. |
| `http-` | HTTP presentation layer. |
| `test-` | Tests. |

| File | When to consult | Content |
| --- | --- | --- |
| [rules/arch-project-structure.md](rules/arch-project-structure.md) | Before creating a project or module, or deciding where a file belongs. | Reference tree of `src/`: modules, `core/`, `gateways/` and `infra/`. |
| [rules/arch-dependency-rule.md](rules/arch-dependency-rule.md) | Before adding imports between layers, or when auditing dependencies. | Imports allowed between `domain`, `application`, `infrastructure`, `presentation` and `core`, including types and factories. |
| [rules/arch-module-isolation.md](rules/arch-module-isolation.md) | Before creating a module, making modules communicate, or moving code to `core/` or `gateways/`. | Boundaries between modules, the `index.ts` public entry point, who may import another module (ports and adapters), references by ID, events, and criteria for sharing code. |
| [rules/arch-conventions.md](rules/arch-conventions.md) | Before naming files and folders, or introducing `utils/`, CQRS or messaging. | Naming, reserved spellings, environment variables and limits on new structures. |
| [rules/arch-request-flow.md](rules/arch-request-flow.md) | When implementing a feature end to end, or when unsure how the layers collaborate. | Example of user registration going through controller, use case, repository and presenter. |
| [rules/domain-layer.md](rules/domain-layer.md) | Before creating or reviewing entities, aggregate roots, value objects or business rules. | Responsibilities and limits of the domain. |
| [rules/domain-organization.md](rules/domain-organization.md) | Before creating entities or value objects, to decide where they live and how to name them. | Purpose of the pattern, primitives to check in the project, file locations, naming and imports. |
| [rules/domain-entity-structure.md](rules/domain-entity-structure.md) | Before creating or reviewing the class of an entity or aggregate root. | Properties interface with `DefaultProps`, inheritance from `AggregateRoot`, `this.props`, dates and `touch()`. |
| [rules/domain-entity-identity.md](rules/domain-entity-identity.md) | Before defining, mapping or reviewing entity identifiers. | Numeric `UniqueEntityID` as the main identity, reserved by the repository's `nextId()` before creation, UUID `PublicEntityID` as an additional property, and generic parameters of the base class. |
| [rules/domain-entity-factory.md](rules/domain-entity-factory.md) | Before writing or changing the `create()` of an entity. | Factory sequence, `Optional`, default values with `??` and handling of dates on construction. |
| [rules/domain-entity-accessors.md](rules/domain-entity-accessors.md) | Before adding getters or setters to an entity. | When to expose getters, when to create setters, and the sequence for updating dates and `touch()`. |
| [rules/domain-entity-example.md](rules/domain-entity-example.md) | When a complete entity model is needed. | `Configuration` example with properties, public ID, getters, a setter and `create()`. |
| [rules/domain-value-objects.md](rules/domain-value-objects.md) | Before creating, changing or reviewing value objects, or deciding between entity and value. | Entity versus value, structure on top of `ValueObject`, construction, named factories, getters and limits of immutability. |
| [rules/domain-value-object-equality.md](rules/domain-value-object-equality.md) | Before comparing value objects or relying on `equals()`. | The `equals()` contract of the base class, comparison through `JSON.stringify` and its limits. |
| [rules/domain-read-projections.md](rules/domain-read-projections.md) | Before modeling read projections or implementing queries that combine data from more than one entity. | When to use projections, the query flow with repository, mapper and presenter, rules and cache caveats, and the `CommentWithAuthor` model. |
| [rules/domain-value-object-examples.md](rules/domain-value-object-examples.md) | When a complete value object model is needed. | `FileUrl` example with `create()` and `ResourceUrl` example with named factories. |
| [rules/domain-checklist.md](rules/domain-checklist.md) | When finishing or reviewing entities and value objects. | Checklist of names, imports, identity, factories, getters, setters, projections and equality. |
| [rules/app-layer.md](rules/app-layer.md) | Before implementing or reviewing use cases, repository contracts or ports. | Shape of use cases, location of contracts, and the strategy for results and errors. |
| [rules/infra-layer.md](rules/infra-layer.md) | Before implementing or reviewing concrete repositories, mappers, schemas or adapters. | Responsibilities of the module infrastructure and of the shared infrastructure in `src/infra/`. |
| [rules/infra-roles.md](rules/infra-roles.md) | Before implementing or reviewing any technical resource or integration, to decide which element to create. | Roles of gateway, infra contract, adapter, factory, `index.ts` and bootstrap, and what each one must not do. |
| [rules/infra-organization.md](rules/infra-organization.md) | Before creating folders or naming files in `src/infra/`, `src/gateways/` or a module's `infrastructure/`. | Reference tree of the shared resources and naming of contracts, gateways, adapters and factories. |
| [rules/infra-contract-placement.md](rules/infra-contract-placement.md) | Before creating a contract or adding imports between application, gateways and infrastructure. | The three kinds of contract (persistence, integration and technical resource), where each one lives, and the allowed imports. |
| [rules/infra-technical-contracts.md](rules/infra-technical-contracts.md) | Before defining the contract of a technical resource, such as the database or the HTTP server. | `DatabaseContract` and `HttpContract` models, lifecycle, and preservation of concrete types. |
| [rules/infra-gateways.md](rules/infra-gateways.md) | Before creating or reviewing a gateway or the technical contract of an integration. | `SecretsGateway` model, handling of missing data and failures, and extension through a technical contract. |
| [rules/infra-adapters.md](rules/infra-adapters.md) | Before implementing or reviewing an adapter for a provider, driver, SDK or framework. | Rules for encapsulation, injection and translation of responses and failures, with an adapter model. |
| [rules/infra-factories.md](rules/infra-factories.md) | Before creating infrastructure factories, exposing an `index.ts`, or deciding the scope of instances. | Factory model, composition, effects on import, singletons, and reuse of pools and clients. |
| [rules/infra-lifecycle.md](rules/infra-lifecycle.md) | Before implementing or changing the bootstrap, startup or shutdown of resources. | Startup and shutdown order, cleanup after failures, and repeated calls to `start()` and `stop()`. |
| [rules/infra-security.md](rules/infra-security.md) | Before dealing with the environment, secrets, timeouts, retries, secret caching, TLS or cryptography. | Rules for configuration, error handling and security of integrations. |
| [rules/http-layer.md](rules/http-layer.md) | Before implementing or reviewing controllers, validation schemas, presenters or use case factories. | Responsibilities of the presentation layer and the rule of keeping schemas in the controller file. |
| [rules/test-organization.md](rules/test-organization.md) | Before creating or reviewing tests. | Location of specs, support code in `test/`, limits of unit and integration tests, and the E2E rule for controllers. |
| [rules/test-domain.md](rules/test-domain.md) | Before creating or reviewing tests for entities or value objects. | The rule of not testing entities and aggregate roots, and what to cover in value objects. |
| [rules/test-infrastructure.md](rules/test-infrastructure.md) | Before testing or reviewing use cases with gateways, adapters, factories or lifecycle. | What to test in each infrastructure element, and a review checklist. |

- When the task involves more than one file, consult the relevant sections of each; do not load every example for a change that does not use them.
- Treat the rules as an implementation reference, not as a justification for widening the scope. TypeScript examples must be adapted to the project's real stack and contracts.
- Respect the project's own instructions. Preserve existing conventions when the task does not require changing them; do not replace primitives or error strategies just to reproduce an example.

## Before implementing

1. Read the project's instructions, such as `AGENTS.md`.
2. Identify the stack, dependencies, validation commands and existing conventions.
3. Examine a similar flow, identify the responsible module and consult the applicable rules.
4. Define the expected behavior, the success and error scenarios, and the affected layers.
5. In new projects, create only the structure that is needed. In existing projects, avoid renames, moves and abstractions outside the scope.

## Essential rules

Read the arrows as allowed imports, not as execution flow:

```text
application    → domain → core
infrastructure → application, domain, core and src/infra
presentation   → application, domain, core and src/infra/http
```

- Keep the domain and application layers free of the ORM, the HTTP framework, the environment and external SDKs, including in type imports. `core/` does not import business modules or infrastructure.
- Put invariants and business behaviors in the domain. Use cases coordinate entities and dependencies through contracts, with explicit input and output and without transport or persistence details.
- Inject concrete implementations at the composition points, such as factories and the bootstrap; not in entities or use cases.
- Organize modules by business capability. Make them communicate through public application contracts or events, never through concrete repositories, schemas or internal details, even when they share the database.
- Resolve environment configuration in the infrastructure and inject the values that are needed; do not commit secrets.

These rules apply to every task. The details of each layer, and of entities, value objects, projections, controllers and infrastructure, are in the `rules/` files; consult them through the index above before implementing.

## Implementation flow

1. Create or adjust the entities, value objects and contracts that are needed, reusing existing ones.
2. Implement the use case with the result and error strategy already adopted; test it with in-memory dependencies or fakes.
3. Implement the persistence, mappers and adapters that are needed.
4. Wire the dependencies at the composition point and implement the controller, validation and presenter.
5. Register controllers, schemas and integrations in the existing mechanism.
6. Run the relevant validations and review the dependencies between layers and modules.

Adapt the sequence to the scope: a small fix does not require recreating the whole flow.

## Validation and review

- Follow the project's test organization; keep unit tests of the domain and application layers independent of the database, the network and the HTTP server.
- Before creating or reviewing tests, consult the `rules/test-*.md` rules: they define what to test in each layer, including the absence of tests for entities and the E2E tests for controllers.
- Test persistence and integrations with controlled dependencies.
- Run the relevant tests, type checks and lint with the existing commands. Do not claim they passed without running them.
- In audits, present paths and evidence. Distinguish dependency violations from naming preferences, and do not treat missing optional folders as failures.
- When finishing, summarize the changes and the validations performed, stating what could not be validated.
