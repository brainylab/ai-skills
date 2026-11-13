# Infraestrutura: gateways e contratos técnicos de integração

O gateway descreve o que a aplicação precisa, sem indicar como o provedor atende à operação:

```ts
// src/gateways/secrets/secrets-gateway.ts
export abstract class SecretsGateway {
  abstract getValue(key: string): Promise<string>;
}
```

Declare gateways e ports como `abstract class` com métodos abstratos. Adapters os estendem com `extends`. Contratos de repositório e contratos técnicos da infra continuam sendo `interface`.

Defina entradas, saídas, ausência de dados e falhas de forma explícita. Neste modelo, a operação retorna uma string ou falha; não retorna `undefined` como se fosse um segredo válido. Em um projeto que utiliza `Either` ou outro resultado tipado, adapte o contrato ao padrão existente.

## Contrato técnico separado

Implemente o gateway diretamente no adapter. Crie um contrato técnico em `src/infra/gateways/<nome>/contracts/` somente quando a implementação precisar de operações técnicas adicionais, como liberar conexões, e reutilize o gateway:

```ts
// src/infra/gateways/secrets/contracts/secrets-contract.ts
import type { SecretsGateway } from "@/gateways/secrets/secrets-gateway";

export interface SecretsContract extends SecretsGateway {
  stop(): Promise<void>;
}
```

Nunca copie a assinatura de `getValue()` em dois contratos independentes. Os métodos técnicos adicionais pertencem ao contrato da infra e ao bootstrap; o caso de uso continua recebendo apenas `SecretsGateway`.
