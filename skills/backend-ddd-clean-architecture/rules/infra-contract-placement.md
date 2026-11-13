# Infrastructure: contract placement and import direction

Distinguish three kinds of contract:

1. **Module persistence:** lives in `modules/<module>/application/repositories/` and describes operations on domain concepts, not SQL queries or tables.
2. **Application integration:** lives in `modules/<module>/application/ports/` when exclusive to the module, or in `src/gateways/` when truly shared. It describes the capability the consumer needs.
3. **Technical resource:** lives in `src/infra/<resource>/contracts/` or in the module's infrastructure. It describes technical operations used by adapters, composition and other infrastructure implementations.

Imports allowed in this pattern:

```text
application/use-case → application/ports or gateways
infra/gateways/adapter → gateway or the technical contract that extends it
infra/factory → adapters, contracts and configuration
infra/app → entry points of the technical resources
infrastructure/repositories → persistence contract and database infrastructure
infrastructure/adapters → port or gateway and technical helpers from infra
presentation/controllers → infra/http (controller helper and HTTP errors)
```

- The domain and application layers never import `src/infra/`, not even with `import type`.
- Application gateways do not import infrastructure contracts. If there is an extension, the direction is `SecretsContract extends SecretsGateway`, never the opposite.
- Do not expose SDK types, provider HTTP responses, ORM clients or configuration objects in gateways and repository contracts.
- A technical database contract may expose the concrete connection through a generic parameter, because its consumers are also infrastructure. That does not allow the type to leak into the application or domain layers.
- Use `import type` for dependencies used only as types.
