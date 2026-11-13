# Infrastructure: factories and entry points

The factory resolves the concrete implementation, receives or obtains the validated configuration, assembles technical dependencies and returns the instance ready to be started or used, according to its contract.

```ts
// src/infra/gateways/secrets/factory/make-secrets-gateway.ts
import type { SecretsGateway } from "@/gateways/secrets/secrets-gateway";
import { ProviderSecretsAdapter } from "../adapters/provider-secrets-adapter";

type SecretsClient = ConstructorParameters<typeof ProviderSecretsAdapter>[0];

export function makeSecretsGateway(client: SecretsClient): SecretsGateway {
  return new ProviderSecretsAdapter(client);
}
```

In this model, the composition point creates the concrete client with the validated configuration and passes it to `makeSecretsGateway(client)`. The factory may also create that client internally when that is the local convention; the use case never does this assembly.

- Factories may import adapters and the environment. Entities and use cases must not import infra factories.
- Use case factories inject repositories and gateways through the constructor. The use case parameter is typed as `SecretsGateway`, not `ProviderSecretsAdapter` and not a technical infra contract.
- Do not call `start()`, run queries or make network calls merely by importing a module. Centralize those effects in the explicit startup or execution flow.
- `index.ts` may export an instance created by the factory when building it does not trigger those effects. Avoid circular dependencies between `index.ts`, the factory and the adapter; internal files import the contract they need directly.
- A singleton is a scope choice, not a requirement of the pattern. Preserve `getInstance()` where it is already adopted and needed, but do not impose it on every adapter.
- Reuse pools and clients according to their cost and lifecycle. Do not create a pool per request, and do not share mutable tenant state between consumers.
- Allow isolated instances in tests; do not make tests depend on the initialization order of global singletons.
