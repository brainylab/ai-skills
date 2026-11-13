# Infraestrutura: adapters

- Declare explicitamente o contrato implementado: `extends` para gateways e ports, que são `abstract class`; `implements` para interfaces, como contratos de repositório e contratos técnicos.
- Encapsule clientes, conexões, cache e detalhes do provedor em membros privados.
- Receba clientes e configuração pelo construtor sempre que possível. Monte-os na factory, sem ler ambiente dentro do caso de uso.
- Converta entradas, respostas e falhas do provedor para o contrato esperado. Valide dados externos antes de tratá-los como confiáveis.
- Não retorne objetos do SDK por conveniência e não use casts para ignorar respostas ausentes ou inválidas.
- Não faça autorização de negócio depender apenas do sucesso de uma chamada técnica.

Modelo de adapter com cliente injetável. `SecretsClient` é uma interface técnica ilustrativa; na implementação real, use a interface necessária do SDK existente ou um wrapper pequeno, sem criar outro port de aplicação para reproduzir o SDK inteiro:

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

O exemplo mostra a tradução de uma resposta sem valor. A implementação real também deve tratar falhas do cliente conforme a estratégia de erros do projeto, sem expor respostas completas do provedor ou segredos. Defina separadamente se uma string vazia é válida para o contrato; não invente essa regra no adapter.
