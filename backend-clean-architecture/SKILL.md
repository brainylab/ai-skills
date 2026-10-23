---
name: backend-clean-architecture
description: Crie, revise ou refatore backends organizados como Modular Monolith com Clean Architecture. Use ao estruturar módulos de negócio, implementar casos de uso, contratos, repositórios, adapters, controllers e testes, ou auditar dependências entre camadas e isolamento entre módulos. Adapte as instruções à stack e às convenções do projeto.
---

# Backend: Modular Monolith com Clean Architecture

## Quando usar

Aplique esta skill para criar módulos e funcionalidades de backend, revisar fronteiras arquiteturais ou executar refatorações solicitadas nessa arquitetura. Não imponha uma migração arquitetural ao corrigir uma tarefa pontual em um projeto com outra organização.

Este arquivo é autocontido: não exige outros documentos, scripts, pacotes ou ferramentas específicas. A árvore usa TypeScript como exemplo; adapte extensões, nomes e testes à linguagem e ao ecossistema do projeto.

## Antes de implementar

1. Leia as instruções do projeto, como `AGENTS.md`, e respeite suas regras específicas.
2. Identifique a linguagem, o framework, o ORM, as dependências, os comandos de validação e as convenções existentes.
3. Examine um fluxo semelhante e determine o módulo responsável pela funcionalidade.
4. Separe o que pertence ao domínio, à aplicação, à infraestrutura e à apresentação antes de editar.
5. Em projetos existentes, preserve nomes e organização compatíveis com a tarefa. Não renomeie camadas, mova módulos ou introduza dependências apenas para reproduzir esta árvore.
6. Em projetos novos, use a estrutura abaixo como referência e crie somente as partes necessárias. Não crie diretórios vazios nem abstrações sem uso.

## Estrutura de referência

```text
src/
├── modules/
│   └── users/                          # módulo de exemplo
│       ├── domain/
│       │   └── entities/               # entidades e aggregate roots
│       │       ├── user.ts
│       │       └── value-objects/
│       │           └── email.ts
│       ├── application/
│       │   ├── repositories/           # contratos de persistência
│       │   │   └── user-repository.ts
│       │   ├── ports/                  # contratos de outros serviços
│       │   │   └── password-hasher.ts
│       │   └── use-case/
│       │       ├── create-user-use-case.ts
│       │       ├── create-user-use-case.spec.ts
│       │       └── errors/
│       ├── infrastructure/
│       │   ├── database/
│       │   │   └── <orm>/
│       │   │       ├── schemas/
│       │   │       ├── repositories/
│       │   │       └── mappers/
│       │   └── adapters/               # implementações dos ports
│       ├── presentation/
│       │   └── http/
│       │       └── controllers/
│       │           ├── create-user-controller.ts
│       │           ├── factories/
│       │           └── presenters/
│       └── test/                       # suporte aos testes
│           ├── factories/
│           └── repositories/           # implementações em memória
├── core/                               # primitivas sem framework
│   ├── entities/
│   ├── errors/
│   ├── events/
│   ├── types/
│   └── use-case/
├── gateways/                           # contratos compartilhados de integrações
├── infra/                              # infraestrutura compartilhada
│   ├── app.ts
│   ├── env.ts
│   ├── config/
│   ├── database/                       # conexão e configuração do banco
│   │   ├── adapters/                   # integração com o driver ou ORM
│   │   │   └── <orm>-adapter.ts
│   │   ├── contracts/                  # contrato técnico de acesso e ciclo de vida
│   │   │   └── database-contract.ts
│   │   ├── factory/                    # criação e configuração do adapter
│   │   │   └── make-database.ts
│   │   └── index.ts                    # ponto de acesso à infraestrutura de banco
│   ├── gateways/
│   ├── messaging/
│   └── http/
│       ├── adapters/                   # integração com o framework HTTP
│       │   └── <framework>-adapter.ts
│       ├── contracts/                  # contrato técnico do servidor HTTP
│       │   └── http-contract.ts
│       ├── factory/                    # criação e configuração do servidor
│       │   └── make-server.ts
│       ├── errors/
│       ├── helpers/
│       ├── routes.ts                   # composição das rotas dos módulos
│       └── index.ts                    # ponto de acesso à infraestrutura HTTP
├── utils/                              # utilitários técnicos
└── main.ts
```

Adapte schemas, relações e migrations às restrições da ferramenta de persistência. Não force organização por módulo quando a ferramenta exigir arquivos centralizados; mantenha a propriedade dos dados e as fronteiras do código explícitas.

## Regras de dependência

Considere as setas como imports permitidos, não como fluxo de execução:

```text
application    → domain → core
infrastructure → application, domain e core
presentation   → application, domain e core
```

- Mantenha `domain/` independente de aplicação, infraestrutura, apresentação, ORM, framework HTTP, ambiente e SDKs externos.
- Mantenha `application/` independente de implementações de infraestrutura e de apresentação.
- Não faça `core/` importar módulos de negócio ou infraestrutura.
- Verifique também imports de tipos: tipos inferidos do ORM não podem vazar para domínio ou aplicação.
- Permita imports de implementações concretas nos pontos de composição, como factories e bootstrap. Não estenda essa exceção a entidades ou casos de uso.
- Permita ao controller usar os recursos HTTP do framework, sem levar esses tipos para as camadas internas.

## Como distribuir as responsabilidades

### Domínio

- Modele conceitos com identidade como entidades, valores com invariantes como value objects e fronteiras de consistência como aggregates, quando necessário.
- Coloque invariantes e comportamentos de negócio no domínio, não em controllers ou consultas de banco.
- Adicione eventos ou serviços de domínio somente quando resolverem uma necessidade concreta.

### Aplicação

- Implemente cada ação como um caso de uso com `execute()` e tipos explícitos de entrada e saída.
- Injete dependências pelo construtor usando contratos, não repositórios concretos ou clientes externos.
- Coloque contratos de persistência em `application/repositories/` e contratos de outros serviços em `application/ports/`.
- Coordene entidades e dependências sem receber objetos de requisição HTTP ou retornar linhas do banco.
- Siga a estratégia de resultados e erros existente, como `Result`/`Either` ou exceções tipadas; não introduza uma estratégia concorrente.
- Deixe códigos HTTP e serialização fora dos casos de uso.

### Infraestrutura

- Implemente os contratos consumidos pela aplicação.
- Mantenha consultas, schemas e mappers específicos dentro do módulo responsável.
- Converta dados entre persistência e domínio nos mappers, sem expor tipos do ORM às camadas internas.
- Centralize conexão de banco, servidor HTTP e recursos técnicos compartilhados em `src/infra/`.
- Organize esses serviços com `adapters/` para implementações concretas, `contracts/` para interfaces técnicas e `factory/` para criação e configuração das instâncias, quando essa separação for necessária.
- Use contratos de infraestrutura para operações técnicas, como iniciar e encerrar conexões ou servidores. Não os use para substituir repositórios e ports da aplicação nem permita que sejam importados pelos casos de uso.
- Resolva e valide configuração de ambiente na infraestrutura e injete os valores necessários. Não versione segredos.
- Mantenha agregadores globais de schemas e relações como detalhes técnicos, nunca como contratos para o domínio.

### Apresentação e composição

- Faça controllers validarem a entrada, chamarem casos de uso e traduzirem os resultados e erros para o transporte.
- Defina todos os schemas de validação do controller no próprio arquivo: `query`, `params`, `body`, `headers`, `response` e outros inputs usados. Não extraia esses schemas para arquivos separados.
- Faça testes de validação reutilizarem os schemas reais do controller, sem duplicar as definições.
- Use presenters para formatar respostas públicas, sem consultas ou regras de negócio. Não exponha hashes, tokens internos ou outros dados sensíveis.
- Use factories ou o mecanismo de composição existente para conectar casos de uso, repositórios e adapters.

### Módulos e código compartilhado

- Organize módulos por capacidade de negócio, não por tabela do banco.
- Não acesse repositórios concretos, schemas ou detalhes internos de outro módulo.
- Para comunicação entre módulos, use contratos públicos de aplicação ou eventos com dados explícitos. Não use tipos do ORM como API de negócio.
- Preserve as fronteiras mesmo quando os módulos compartilham o banco de dados.
- Coloque em `core/` apenas conceitos realmente compartilhados e independentes de infraestrutura.
- Use `gateways/` para contratos de integrações compartilhadas; mantenha contratos exclusivos no módulo consumidor.
- Não coloque regras de negócio em `utils/` nem mova conceitos para o código compartilhado apenas para facilitar imports.

## Fluxo de implementação

1. Defina o comportamento esperado e os cenários de sucesso e erro.
2. Crie ou ajuste as entidades, value objects e contratos necessários, reutilizando os existentes.
3. Implemente o caso de uso e teste-o com dependências em memória ou fakes.
4. Implemente ou ajuste a persistência, os mappers e os adapters necessários.
5. Conecte as dependências na composição e implemente controller, validação e presenter.
6. Registre rotas, schemas e integrações no mecanismo já utilizado pelo projeto.
7. Execute as validações relevantes e revise os imports entre camadas.

Por exemplo, no cadastro de usuário, faça o controller validar a entrada e chamar `CreateUserUseCase`. O caso de uso consulta `UserRepository`, usa `PasswordHasher`, cria `User` respeitando suas invariantes e solicita a persistência. Retorne uma saída explícita sem credenciais. O presenter formata a saída e o controller define a resposta HTTP. Injete as implementações concretas na factory e use substitutos controlados nos testes.

## Testes e revisão

- Mantenha `*.spec.ts` próximos da unidade testada, ou siga a convenção equivalente do projeto.
- Use `test/factories/` para objetos de teste e `test/repositories/` para repositórios em memória.
- Não exija banco, rede ou servidor nos testes unitários de domínio e aplicação.
- Teste repositórios concretos e integrações com dependências controladas em testes de integração.
- Cubra validação, tradução de erros e formato das respostas nos testes HTTP.
- Execute testes, checagem de tipos e lint relevantes com os comandos existentes; não afirme que passaram sem executá-los.
- Em auditorias, apresente caminhos e evidências concretas. Diferencie violações de dependência de preferências de nomenclatura e organização.
- Não trate ausência de pastas opcionais como falha arquitetural. Introduza CQRS, eventos ou mensageria apenas quando necessários.
- Ao concluir, resuma as alterações e as validações realizadas, ou explique o que não foi possível validar.
