# Infrastructure: gateways and technical integration contracts

The gateway describes what the application needs, without saying how the provider fulfills the operation:

```ts
// src/gateways/secrets/secrets-gateway.ts
export abstract class SecretsGateway {
  abstract getValue(key: string): Promise<string>;
}
```

Declare gateways and ports as an `abstract class` with abstract methods. Adapters extend them with `extends`. Repository contracts and technical infra contracts remain an `interface`.

Define inputs, outputs, missing data and failures explicitly. In this model, the operation returns a string or fails; it does not return `undefined` as if it were a valid secret. In a project that uses `Either` or another typed result, adapt the contract to the existing pattern.

## Separate technical contract

Implement the gateway directly in the adapter. Create a technical contract in `src/infra/gateways/<name>/contracts/` only when the implementation needs additional technical operations, such as releasing connections, and reuse the gateway:

```ts
// src/infra/gateways/secrets/contracts/secrets-contract.ts
import type { SecretsGateway } from "@/gateways/secrets/secrets-gateway";

export interface SecretsContract extends SecretsGateway {
  stop(): Promise<void>;
}
```

Never copy the `getValue()` signature into two independent contracts. The additional technical methods belong to the infra contract and to the bootstrap; the use case still receives only `SecretsGateway`.
