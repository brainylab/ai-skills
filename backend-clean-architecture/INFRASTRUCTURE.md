# Padrão de infraestrutura e gateways

## Objetivo

Implemente recursos técnicos com separação entre **contracts**, **adapters** e **factories**, mantendo os casos de uso independentes de drivers, frameworks, configuração e SDKs externos.

Use este guia junto de [STRUCTURE.md](STRUCTURE.md) ao implementar banco de dados, HTTP, mensageria ou integrações. Os exemplos em TypeScript são modelos estruturais: adapte nomes, aliases, assinaturas e estratégia de erros ao projeto, sem criar abstrações ou recursos sem necessidade.

## 1. Responsabilidades

| Elemento | Responsabilidade | Não deve fazer |
| --- | --- | --- |
| Gateway | Definir a capacidade de uma integração necessária à aplicação. | Expor SDKs, ambiente, clientes concretos ou ciclo de vida técnico ao caso de uso. |
| Contract da infra | Definir a interface técnica de um recurso, como conexão, servidor e ciclo de vida. | Substituir contratos de aplicação ou carregar regras de negócio. |
| Adapter | Implementar um contrato utilizando uma tecnologia concreta. | Coordenar casos de uso, autorizar operações de negócio ou responder HTTP fora do adapter HTTP. |
| Factory | Escolher implementações e montar suas dependências e configurações. | Executar regras de negócio ou iniciar recursos implicitamente. |
| `index.ts` | Expor o ponto de acesso do recurso e os tipos técnicos necessários. | Duplicar contratos, esconder inicialização assíncrona ou tornar infraestrutura acessível às camadas internas. |
| Bootstrap | Coordenar inicialização, encerramento e tratamento de falhas dos recursos. | Conter regras dos módulos de negócio. |

Um gateway é um contrato de saída da aplicação, não um servidor HTTP nem necessariamente uma classe concreta. Um adapter implementa esse contrato e traduz as operações para o provedor escolhido.

## 2. Organização e nomenclatura

Estrutura de referência para recursos compartilhados:

```text
src/
├── gateways/
│   └── secrets/
│       └── secrets.ts                   # SecretsGateway: contrato da aplicação
├── infra/
│   ├── app.ts                           # composição do ciclo de vida
│   ├── env.ts                           # leitura e validação do ambiente
│   ├── database/
│   │   ├── contracts/
│   │   │   └── database-contract.ts
│   │   ├── adapters/
│   │   │   └── <driver>-adapter.ts
│   │   ├── factory/
│   │   │   └── make-database.ts
│   │   └── index.ts
│   ├── http/
│   │   ├── contracts/
│   │   │   └── http-contract.ts
│   │   ├── adapters/
│   │   │   └── <framework>-adapter.ts
│   │   ├── factory/
│   │   │   └── make-server.ts
│   │   └── index.ts
│   └── gateways/
│       └── secrets/
│           ├── contracts/
│           │   └── secrets.ts           # contrato técnico, quando necessário
│           ├── adapter/
│           │   └── <provider>-secrets.ts
│           ├── factories/
│           │   └── make-secrets.ts
│           └── index.ts
└── modules/
    └── <modulo>/
        ├── application/
        │   ├── ports/                   # contratos exclusivos do módulo
        │   └── repositories/            # contratos de persistência do módulo
        └── infrastructure/              # implementações exclusivas do módulo
```

- Use arquivos em `kebab-case`, contratos e classes em `PascalCase` e factories com prefixo `make`.
- Nomeie contratos técnicos como `DatabaseContract` e `HttpContract`; gateways como `SecretsGateway`; adapters pela tecnologia e capacidade, como `AwsSmSecretsAdapter`.
- Preserve a convenção local de singular/plural das pastas. No `server-credentials`, banco e HTTP usam `adapters/` e `factory/`; gateways usam `adapter/` e `factories/`.
- Use a grafia `infrastructure/` para a infraestrutura exclusiva dos módulos. Reserve `src/infra/` para a infraestrutura compartilhada, conforme `STRUCTURE.md`.
- Não crie diretórios vazios. Um gateway não exige uma camada técnica adicional quando seu próprio contrato já atende à implementação.

## 3. Localização dos contratos e direção dos imports

Distinga três tipos de contrato:

1. **Persistência do módulo:** fica em `modules/<modulo>/application/repositories/` e descreve operações sobre conceitos do domínio, não consultas SQL ou tabelas.
2. **Integração da aplicação:** fica em `modules/<modulo>/application/ports/` quando exclusiva do módulo, ou em `src/gateways/` quando realmente compartilhada. Descreve a capacidade necessária ao consumidor.
3. **Recurso técnico:** fica em `src/infra/<recurso>/contracts/` ou na infraestrutura do módulo. Descreve operações técnicas usadas por adapters, composição e outras implementações de infraestrutura.

Imports permitidos neste padrão:

```text
application/use-case → application/ports ou gateways
infra/gateways/adapter → gateway ou contrato técnico que o estende
infra/factory → adapters, contracts e configuração
infra/app → pontos de acesso dos recursos técnicos
infrastructure/repositories → contrato de persistência e infraestrutura de banco
```

- Domínio e aplicação nunca importam `src/infra/`, mesmo com `import type`.
- Gateways da aplicação não importam contratos de infraestrutura. Se houver extensão, a direção é `SecretsContract extends SecretsGateway`, nunca o contrário.
- Não exponha tipos do SDK, respostas HTTP do provedor, clientes de ORM ou objetos de configuração nos gateways e contratos de repositório.
- Um contrato técnico de banco pode expor a conexão concreta por um parâmetro genérico, pois seus consumidores também são infraestrutura. Isso não autoriza o vazamento desse tipo para aplicação ou domínio.
- Use `import type` para dependências utilizadas apenas como tipos.

## 4. Contracts técnicos

Defina somente as operações exigidas pelo recurso. Banco e servidor HTTP podem seguir estes modelos:

```ts
export interface DatabaseContract<TConnection = unknown> {
  start(): Promise<void>;
  stop(): Promise<void>;
  connection(): TConnection;
}

export interface HttpContract<TServer = unknown> {
  start(): Promise<void>;
  stop(): Promise<void>;
  server(): TServer;
}
```

- O adapter implementa o contrato com o tipo concreto da tecnologia.
- Defina o comportamento de `connection()` e `server()` antes da inicialização e após o encerramento; não retorne uma referência inválida silenciosamente.
- A obtenção de uma referência não deve esconder conexão assíncrona. Deixe a inicialização explícita em `start()` quando esse for o ciclo de vida adotado.
- Não imponha `start()` e `stop()` a integrações sem recursos a inicializar ou liberar.
- Não duplique tipos auxiliares entre `contracts/` e `index.ts`. Declare-os em um único lugar e reexporte quando necessário.
- Preserve tipos concretos necessários aos repositórios. Uma factory de banco pode retornar `DatabaseContract<ConcreteConnection>` ou manter a inferência do adapter; não apague o tipo da conexão com uma anotação genérica que o reduza a `unknown`.

## 5. Gateways e contratos técnicos de integração

O gateway descreve o que a aplicação precisa, sem indicar como o provedor atende à operação:

```ts
// src/gateways/secrets/secrets.ts
export interface SecretsGateway {
  getValue(key: string): Promise<string>;
}
```

Defina entradas, saídas, ausência de dados e falhas de forma explícita. Neste modelo, a operação retorna uma string ou falha; não retorna `undefined` como se fosse um segredo válido. Em um projeto que utiliza `Either` ou outro resultado tipado, adapte o contrato ao padrão existente.

Quando o projeto mantiver um contrato técnico separado, reutilize o gateway:

```ts
// src/infra/gateways/secrets/contracts/secrets.ts
import type { SecretsGateway } from "@/gateways/secrets/secrets";

export interface SecretsContract extends SecretsGateway {}
```

Essa extensão preserva a convenção existente do `server-credentials`, mas não cria uma capacidade nova. Em código novo, implemente o gateway diretamente quando não houver necessidade de um contrato técnico separado. Nunca copie a assinatura de `getValue()` em dois contratos independentes.

Se houver métodos técnicos adicionais, como liberar conexões, eles pertencem ao contrato da infra e ao bootstrap; o caso de uso continua recebendo apenas `SecretsGateway`.

## 6. Adapters

- Implemente explicitamente o contrato com `implements`.
- Encapsule clientes, conexões, cache e detalhes do provedor em membros privados.
- Receba clientes e configuração pelo construtor sempre que possível. Monte-os na factory, sem ler ambiente dentro do caso de uso.
- Converta entradas, respostas e falhas do provedor para o contrato esperado. Valide dados externos antes de tratá-los como confiáveis.
- Não retorne objetos do SDK por conveniência e não use casts para ignorar respostas ausentes ou inválidas.
- Não faça autorização de negócio depender apenas do sucesso de uma chamada técnica.

Modelo de adapter com cliente injetável. `SecretsClient` é uma interface técnica ilustrativa; na implementação real, use a interface necessária do SDK existente ou um wrapper pequeno, sem criar outro port de aplicação para reproduzir o SDK inteiro:

```ts
import type { SecretsContract } from "../contracts/secrets";

interface SecretsClient {
  read(key: string): Promise<{ value?: string }>;
}

export class ProviderSecretsAdapter implements SecretsContract {
  constructor(private readonly client: SecretsClient) {}

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

## 7. Factories e pontos de acesso

A factory resolve a implementação concreta, recebe ou obtém a configuração validada, monta dependências técnicas e retorna a instância pronta para ser inicializada ou usada, conforme seu contrato.

```ts
// src/infra/gateways/secrets/factories/make-secrets.ts
import type { SecretsContract } from "../contracts/secrets";
import { ProviderSecretsAdapter } from "../adapter/provider-secrets";

type SecretsClient = ConstructorParameters<typeof ProviderSecretsAdapter>[0];

export function makeSecrets(client: SecretsClient): SecretsContract {
  return new ProviderSecretsAdapter(client);
}
```

Neste modelo, o ponto de composição cria o cliente concreto com a configuração validada e o passa a `makeSecrets(client)`. A factory também pode criar esse cliente internamente quando essa for a convenção local; o caso de uso nunca faz essa montagem.

- Factories podem importar adapters e ambiente. Entidades e casos de uso não podem importar factories da infra.
- Factories de casos de uso injetam repositórios e gateways pelo construtor. O parâmetro do caso de uso usa `SecretsGateway`, não `ProviderSecretsAdapter` ou `SecretsContract`.
- Não chame `start()`, execute consultas ou faça chamadas de rede apenas ao importar um módulo. Centralize esses efeitos no fluxo explícito de inicialização ou execução.
- `index.ts` pode exportar uma instância criada pela factory quando sua construção não iniciar esses efeitos. Evite dependências circulares entre `index.ts`, factory e adapter; arquivos internos importam diretamente o contrato necessário.
- Singleton é uma escolha de escopo, não requisito do padrão. Preserve `getInstance()` onde já for adotado e necessário, mas não o imponha a todos os adapters.
- Reutilize pools e clientes conforme seu custo e ciclo de vida. Não crie um pool por requisição nem compartilhe estado mutável de tenant entre consumidores.
- Permita instâncias isoladas em testes; não faça testes dependerem da ordem de inicialização de singletons globais.

## 8. Inicialização e encerramento

- O bootstrap inicializa dependências antes dos seus consumidores: por exemplo, banco antes de aceitar requisições HTTP.
- Aguarde cada inicialização e propague falhas; não anuncie disponibilidade antes de todos os recursos obrigatórios estarem prontos.
- Se uma etapa falhar, libere os recursos já iniciados. Um recurso composto também deve limpar sua própria inicialização parcial.
- Encerre em ordem inversa, deixando de aceitar novas requisições antes de fechar dependências utilizadas por elas.
- Uma falha ao encerrar um recurso não deve impedir a tentativa de liberar os demais. Preserve a informação das falhas sem registrar dados sensíveis.
- Defina o comportamento de chamadas repetidas de `start()` e `stop()` e teste-o. Não presuma idempotência de um driver ou framework.
- Mantenha sinais e código de saída em `main.ts`; detalhes de conexão e servidor pertencem aos adapters.

Essas são diretrizes de implementação, não uma afirmação de que todos esses controles já existem no serviço.

## 9. Erros, configuração e segurança

- Valide ambiente centralmente e injete somente os valores necessários a cada dependência.
- Não versione segredos nem registre credenciais, chaves, tokens, URLs autenticadas ou respostas completas do provedor.
- Defina timeouts e, quando necessários, retries limitados e compatíveis com a idempotência da operação. Não repita operações indefinidamente nem esconda indisponibilidade com valores padrão.
- Quando houver cache de segredos, defina validade, invalidação, isolamento e relação com rotação e revogação. Cache não pode ignorar uma exigência de revogação imediata.
- Não enfraqueça TLS, validação de identidade ou autorização para contornar erros de integração.
- No `server-credentials`, integrações de execução devem funcionar sem acesso à internet. A presença do adapter AWS não comprova conectividade privada disponível ou aprovada.
- Mudanças em criptografia ou armazenamento de segredos exigem considerar compatibilidade, integridade, gerenciamento de chaves e migração; não copie algoritmos de exemplo como decisão de segurança.

## 10. Testes e checklist

- Teste casos de uso com fakes dos gateways e repositórios em memória, sem importar factories, ambiente, SDKs, rede ou banco real.
- Teste adapters com clientes controlados para cobrir tradução de dados, respostas inválidas e falhas do provedor; use integração controlada para validar o comportamento real do driver ou SDK quando necessário.
- Teste factories relevantes quanto à seleção e configuração das dependências, sem exigir rede apenas para construir uma instância.
- Cubra ciclo de vida, inicialização parcial, limpeza após falhas e encerramento dos recursos.
- Valide controllers e seus schemas somente pelos testes E2E HTTP, conforme [STRUCTURE.md](STRUCTURE.md), sem criar testes isolados de controller.
- Confira se nenhum contrato de aplicação importa tipos técnicos e se nenhum caso de uso instancia implementações concretas.
- Confira se a factory preserva os tipos necessários à infraestrutura sem expô-los às camadas internas.
- Execute as validações existentes pertinentes à mudança e remova apenas os artefatos temporários gerados por sua própria validação.
