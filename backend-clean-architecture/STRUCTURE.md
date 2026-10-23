# Guideline de estrutura para backends

## Modular Monolith com Clean Architecture

Organize o backend em módulos de negócio dentro de uma única aplicação. Cada módulo separa regras de negócio, casos de uso, detalhes técnicos e interfaces de entrada.

O objetivo é manter as regras de negócio independentes de banco de dados, frameworks e serviços externos, permitindo testar e substituir essas tecnologias sem reescrever os casos de uso.

## Estrutura de referência

Exemplo em TypeScript com um módulo `users`:

```text
src/
├── modules/
│   └── users/
│       ├── domain/
│       │   └── entities/                 # entidades e aggregate roots
│       │       ├── user.ts
│       │       └── value-objects/
│       │           └── email.ts
│       │
│       ├── application/
│       │   ├── repositories/             # contratos de persistência
│       │   │   └── user-repository.ts
│       │   ├── ports/                    # contratos de serviços externos
│       │   │   └── password-hasher.ts
│       │   └── use-case/
│       │       ├── create-user-use-case.ts
│       │       ├── create-user-use-case.spec.ts
│       │       └── errors/
│       │           └── email-already-in-use-error.ts
│       │
│       ├── infrastructure/
│       │   ├── database/
│       │   │   └── <orm>/                # por exemplo: drizzle ou prisma
│       │   │       ├── schemas/
│       │   │       ├── repositories/
│       │   │       └── mappers/
│       │   └── adapters/                 # implementações de ports do módulo
│       │
│       ├── presentation/
│       │   └── http/
│       │       └── controllers/
│       │           ├── create-user-controller.ts
│       │           ├── factories/
│       │           │   └── make-create-user-use-case.ts
│       │           └── presenters/
│       │               └── user-presenter.ts
│       │
│       └── test/                         # suporte aos testes do módulo
│           ├── factories/
│           │   └── make-user.ts
│           └── repositories/
│               └── in-memory-user-repository.ts
│
├── core/                                 # primitivas compartilhadas sem framework
│   ├── entities/                         # Entity, AggregateRoot, ValueObject, IDs
│   ├── errors/
│   ├── events/
│   ├── types/
│   └── use-case/                         # tipos comuns de entrada e saída
│
├── gateways/                             # contratos de integrações compartilhadas
│
├── infra/                                # infraestrutura compartilhada
│   ├── app.ts                            # inicialização e encerramento dos serviços
│   ├── env.ts                            # leitura e validação do ambiente
│   ├── config/
│   ├── database/                         # conexão e configuração do banco
│   │   ├── adapters/                     # integração com o driver ou ORM
│   │   │   └── <orm>-adapter.ts
│   │   ├── contracts/                    # contrato técnico de acesso e ciclo de vida
│   │   │   └── database-contract.ts
│   │   ├── factory/                      # criação e configuração do adapter
│   │   │   └── make-database.ts
│   │   └── index.ts                      # ponto de acesso à infraestrutura de banco
│   ├── gateways/                         # implementações dos contratos compartilhados
│   ├── messaging/                        # conexão e configuração de mensageria
│   └── http/
│       ├── adapters/                     # integração com o framework HTTP
│       │   └── <framework>-adapter.ts
│       ├── contracts/                    # contrato técnico do servidor HTTP
│       │   └── http-contract.ts
│       ├── factory/                      # criação e configuração do servidor
│       │   └── make-server.ts
│       ├── errors/
│       ├── helpers/
│       ├── routes.ts                     # composição das rotas dos módulos
│       └── index.ts                      # ponto de acesso à infraestrutura HTTP
│
├── utils/                                # utilitários técnicos compartilhados
└── main.ts                               # entrada e ciclo de vida do processo
```

A árvore é uma referência, não uma lista de pastas obrigatórias. Crie apenas as estruturas necessárias. Adapte arquivos de schemas, relações e migrations à ferramenta de persistência escolhida.

## Regra de dependência

As dependências apontam para as camadas internas:

```text
application    → domain → core
infrastructure → application, domain e core
presentation   → application, domain e core
```

- `domain/` não depende de `application/`, `infrastructure/`, `presentation/` ou `infra/`.
- `application/` não depende de infraestrutura ou apresentação.
- `core/` não depende de módulos de negócio nem de infraestrutura.
- Factories são pontos de composição: podem importar implementações concretas para injetá-las nos casos de uso.
- A regra também vale para imports de tipos: um tipo inferido do ORM continua sendo uma dependência de infraestrutura.

## Responsabilidades por camada

### Domínio — regras de negócio

- Entidades representam conceitos com identidade; aggregate roots delimitam grupos de objetos cuja consistência é controlada em conjunto.
- Value objects representam valores e suas invariantes, como um e-mail válido.
- Regras de negócio ficam no domínio, não em controllers ou repositórios de banco.
- O domínio não conhece requisições HTTP, ORM, variáveis de ambiente ou SDKs externos.
- Adicione eventos e serviços de domínio quando houver necessidade, sem criar estruturas vazias antecipadamente.

### Aplicação — casos de uso e contratos

- Cada caso de uso representa uma ação do sistema e expõe um método `execute()` com entrada e saída explícitas.
- Recebe dependências pelo construtor, usando contratos em vez de implementações concretas.
- Contratos de persistência ficam em `application/repositories/`; contratos de outros serviços necessários ao módulo ficam em `application/ports/`.
- Coordena entidades e dependências, sem conhecer detalhes do transporte ou do banco.
- Retorna resultados e erros da aplicação; códigos HTTP são definidos na apresentação.
- Adote uma estratégia consistente para resultados e erros, como `Result`/`Either` ou exceções tipadas.

### Infraestrutura — implementações técnicas

- Implementa repositórios e ports definidos pelas camadas internas.
- Contém consultas, schemas, mappers e integrações com ferramentas externas.
- Mappers convertem dados de persistência em objetos do domínio e vice-versa, evitando expor tipos do ORM às camadas internas.
- Detalhes exclusivos de um módulo ficam em sua `infrastructure/`; conexão de banco, servidor HTTP e outros recursos compartilhados ficam em `src/infra/`.
- Configuração de ambiente é resolvida na infraestrutura e os valores necessários são injetados nas dependências.
- Organize os serviços técnicos compartilhados com `adapters/` para implementações concretas, `contracts/` para interfaces técnicas e `factory/` para criação e configuração das instâncias, quando essa separação for necessária.
- Contratos de infraestrutura descrevem operações técnicas, como iniciar e encerrar conexões ou servidores. Eles não substituem os contratos de repositórios e ports da aplicação e não devem ser importados pelos casos de uso.
- Quando a ferramenta exigir agregadores globais de schemas ou relações, mantenha-os em `infra/database/`, sem torná-los contratos do domínio.

### Apresentação — entrada e saída

- Controllers validam a requisição, chamam casos de uso e traduzem o resultado para o transporte.
- Todos os schemas de validação de um controller (`query`, `params`, `body`, `headers` e `response`) ficam no próprio arquivo do controller.
- Não crie arquivos separados para schemas dos controllers. Testes de validação devem reutilizar esses schemas, sem duplicar suas definições.
- Presenters transformam os resultados em respostas públicas, sem executar consultas ou regras de negócio.
- Factories conectam casos de uso, repositórios e adapters concretos.
- O framework HTTP pode ser usado na apresentação e na infraestrutura HTTP, nunca no domínio ou na aplicação.

## Isolamento entre módulos

- Organize módulos por capacidade de negócio, como `users`, `orders` e `billing`, não por tabela do banco.
- Um módulo não acessa repositórios concretos, schemas ou detalhes internos de outro módulo.
- Quando houver comunicação entre módulos, exponha contratos de aplicação ou eventos com dados explícitos.
- Compartilhar o mesmo banco não elimina as fronteiras entre módulos.
- Use `core/` apenas para conceitos realmente compartilhados. Não mova regras específicas de um módulo para lá para facilitar imports.
- Contratos de integrações usados por vários módulos podem ficar em `gateways/`; contratos exclusivos permanecem no módulo consumidor.

## Testes

- Mantenha arquivos `*.spec.ts` próximos da unidade testada.
- Use `test/factories/` para construir objetos e dados de teste e `test/repositories/` para repositórios em memória.
- Testes unitários de domínio e aplicação não devem exigir banco, rede ou servidor HTTP.
- Teste repositórios concretos e integrações em testes de integração, com dependências controladas.
- Cubra validação, tradução de erros e formato das respostas nos testes HTTP.

## Exemplo de fluxo

Para cadastrar um usuário:

1. `CreateUserController` valida a entrada e chama `CreateUserUseCase`.
2. `CreateUserUseCase` consulta o contrato `UserRepository` para verificar se o e-mail já existe.
3. O caso de uso utiliza `PasswordHasher` para gerar o hash e cria a entidade `User`, respeitando as invariantes do domínio.
4. A implementação concreta de `UserRepository` converte os dados e persiste o usuário.
5. O caso de uso retorna uma saída explícita, sem credenciais; o presenter formata essa saída e o controller responde com o status HTTP adequado.

A factory injeta o repositório e o gerador de hash concretos no caso de uso. Nos testes unitários, essas dependências são substituídas por implementações em memória ou fakes.

## Convenções gerais

- Use nomes de arquivos em `kebab-case` e nomes que expressem a responsabilidade de cada unidade.
- Mantenha as grafias `infrastructure` e `presentation`; neste modelo, `infra/` é a abreviação reservada à infraestrutura compartilhada.
- Não coloque regras de negócio em `utils/`. Utilitários das camadas internas devem continuar livres de dependências técnicas.
- Centralize a validação de variáveis de ambiente e não versione segredos.
- Separe `commands/` e `queries/` ou introduza mensageria somente quando a complexidade justificar.
- Priorize fronteiras claras e dependências corretas, não a quantidade de diretórios.
