# Infraestrutura: factories e pontos de acesso

A factory resolve a implementação concreta, recebe ou obtém a configuração validada, monta dependências técnicas e retorna a instância pronta para ser inicializada ou usada, conforme seu contrato.

```ts
// src/infra/gateways/secrets/factory/make-secrets-gateway.ts
import type { SecretsGateway } from "@/gateways/secrets/secrets-gateway";
import { ProviderSecretsAdapter } from "../adapters/provider-secrets-adapter";

type SecretsClient = ConstructorParameters<typeof ProviderSecretsAdapter>[0];

export function makeSecretsGateway(client: SecretsClient): SecretsGateway {
  return new ProviderSecretsAdapter(client);
}
```

Neste modelo, o ponto de composição cria o cliente concreto com a configuração validada e o passa a `makeSecretsGateway(client)`. A factory também pode criar esse cliente internamente quando essa for a convenção local; o caso de uso nunca faz essa montagem.

- Factories podem importar adapters e ambiente. Entidades e casos de uso não podem importar factories da infra.
- Factories de casos de uso injetam repositórios e gateways pelo construtor. O parâmetro do caso de uso usa `SecretsGateway`, não `ProviderSecretsAdapter` nem um contrato técnico da infra.
- Não chame `start()`, execute consultas ou faça chamadas de rede apenas ao importar um módulo. Centralize esses efeitos no fluxo explícito de inicialização ou execução.
- `index.ts` pode exportar uma instância criada pela factory quando sua construção não iniciar esses efeitos. Evite dependências circulares entre `index.ts`, factory e adapter; arquivos internos importam diretamente o contrato necessário.
- Singleton é uma escolha de escopo, não requisito do padrão. Preserve `getInstance()` onde já for adotado e necessário, mas não o imponha a todos os adapters.
- Reutilize pools e clientes conforme seu custo e ciclo de vida. Não crie um pool por requisição nem compartilhe estado mutável de tenant entre consumidores.
- Permita instâncias isoladas em testes; não faça testes dependerem da ordem de inicialização de singletons globais.
