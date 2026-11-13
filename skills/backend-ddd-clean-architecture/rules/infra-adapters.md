# Infrastructure: adapters

- Declare explicitly which contract is implemented: `extends` for gateways and ports, which are an `abstract class`; `implements` for interfaces, such as repository contracts and technical contracts.
- Encapsulate clients, connections, cache and provider details in private members.
- Receive clients and configuration through the constructor whenever possible. Assemble them in the factory, without reading the environment inside the use case.
- Convert provider inputs, responses and failures to the expected contract. Validate external data before treating it as trusted.
- Do not return SDK objects for convenience, and do not use casts to ignore missing or invalid responses.
- Do not make business authorization depend solely on the success of a technical call.

Model of an adapter with an injectable client. `SecretsClient` is an illustrative technical interface; in a real implementation, use the required interface of the existing SDK or a small wrapper, without creating another application port that mirrors the whole SDK:

```ts
import { SecretsGateway } from "@/gateways/secrets/secrets-gateway";

interface SecretsClient {
  read(key: string): Promise<{ value?: string }>;
}

export class ProviderSecretsAdapter extends SecretsGateway {
  constructor(private readonly client: SecretsClient) {
    super();
  }

  async getValue(key: string): Promise<string> {
    const result = await this.client.read(key);

    if (result.value === undefined) {
      throw new Error("Secret unavailable.");
    }

    return result.value;
  }
}
```

The example shows the translation of a response with no value. A real implementation must also handle client failures according to the project's error strategy, without exposing full provider responses or secrets. Decide separately whether an empty string is valid for the contract; do not invent that rule in the adapter.
