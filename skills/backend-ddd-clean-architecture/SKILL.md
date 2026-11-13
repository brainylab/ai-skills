---
name: backend-ddd-clean-architecture
description: Crie, revise ou refatore backends organizados como Modular Monolith com Clean Architecture. Use ao estruturar módulos de negócio, implementar entidades, casos de uso, contratos, repositórios, adapters, controllers e testes, ou auditar dependências entre camadas e isolamento entre módulos. Consulte as regras em `rules/` conforme a tarefa e adapte as instruções à stack e às convenções do projeto.
---

# Backend: Modular Monolith com Clean Architecture

## Escopo

Aplique esta skill ao implementar funcionalidades, revisar fronteiras ou refatorar backends nessa arquitetura. Não imponha uma migração arquitetural ao corrigir uma tarefa pontual em um projeto com outra organização.

## Regras sob demanda

Os detalhes e exemplos ficam nos arquivos ao lado desta skill. Resolva os caminhos abaixo a partir da pasta que contém este `SKILL.md`, não da raiz do projeto de destino. Consulte apenas os arquivos e seções relevantes à tarefa.

### Regras (`rules/`)

Cada arquivo cobre um único tema e pode ser lido isoladamente. O prefixo do nome indica a área:

| Prefixo | Área |
| --- | --- |
| `arch-` | Arquitetura geral: estrutura, dependências, módulos e convenções. |
| `domain-` | Camada de domínio. |
| `app-` | Camada de aplicação. |
| `infra-` | Camada de infraestrutura. |
| `http-` | Camada de apresentação HTTP. |
| `test-` | Testes. |

| Arquivo | Quando consultar | Conteúdo |
| --- | --- | --- |
| [rules/arch-project-structure.md](rules/arch-project-structure.md) | Antes de criar um projeto ou módulo ou decidir onde um arquivo deve ficar. | Árvore de referência de `src/`: módulos, `core/`, `gateways/` e `infra/`. |
| [rules/arch-dependency-rule.md](rules/arch-dependency-rule.md) | Antes de adicionar imports entre camadas ou ao auditar dependências. | Imports permitidos entre `domain`, `application`, `infrastructure`, `presentation` e `core`, inclusive para tipos e factories. |
| [rules/arch-module-isolation.md](rules/arch-module-isolation.md) | Antes de criar um módulo, fazer módulos se comunicarem ou mover código para `core/` ou `gateways/`. | Fronteiras entre módulos, comunicação por contratos ou eventos e critérios para compartilhar código. |
| [rules/arch-conventions.md](rules/arch-conventions.md) | Antes de nomear arquivos e pastas ou introduzir `utils/`, CQRS ou mensageria. | Nomenclatura, grafias reservadas, variáveis de ambiente e limites para novas estruturas. |
| [rules/arch-request-flow.md](rules/arch-request-flow.md) | Ao implementar uma funcionalidade de ponta a ponta ou quando houver dúvida sobre como as camadas colaboram. | Exemplo do cadastro de usuário passando por controller, caso de uso, repositório e presenter. |
| [rules/domain-layer.md](rules/domain-layer.md) | Antes de criar ou revisar entidades, aggregate roots, value objects ou regras de negócio. | Responsabilidades e limites do domínio. |
| [rules/domain-organization.md](rules/domain-organization.md) | Antes de criar entidades ou value objects, para decidir onde ficam e como nomeá-los. | Objetivo do padrão, primitivas a conferir no projeto, localização dos arquivos, nomenclatura e imports. |
| [rules/domain-entity-structure.md](rules/domain-entity-structure.md) | Antes de criar ou revisar a classe de uma entidade ou aggregate root. | Interface de propriedades com `DefaultProps`, herança de `AggregateRoot`, `this.props`, datas e `touch()`. |
| [rules/domain-entity-identity.md](rules/domain-entity-identity.md) | Antes de definir, mapear ou revisar identificadores de entidades. | `UniqueEntityID` numérico como identidade principal, `PublicEntityID` UUID como propriedade adicional e parâmetros genéricos da base. |
| [rules/domain-entity-factory.md](rules/domain-entity-factory.md) | Antes de escrever ou alterar o `create()` de uma entidade. | Sequência da fábrica, `Optional`, valores padrão com `??` e tratamento de datas na construção. |
| [rules/domain-entity-accessors.md](rules/domain-entity-accessors.md) | Antes de adicionar getters ou setters a uma entidade. | Quando expor getters, quando criar setters e a sequência de atualização de datas e `touch()`. |
| [rules/domain-entity-example.md](rules/domain-entity-example.md) | Ao precisar de um modelo completo de entidade. | Exemplo `Configuration` com propriedades, ID público, getters, setter e `create()`. |
| [rules/domain-value-objects.md](rules/domain-value-objects.md) | Antes de criar, alterar ou revisar value objects ou decidir entre entidade e valor. | Entidade versus valor, estrutura sobre `ValueObject`, construção, fábricas nomeadas, getters e limites de imutabilidade. |
| [rules/domain-value-object-equality.md](rules/domain-value-object-equality.md) | Antes de comparar value objects ou depender de `equals()`. | Contrato de `equals()` da base, comparação por `JSON.stringify` e seus limites. |
| [rules/domain-read-projections.md](rules/domain-read-projections.md) | Antes de modelar projeções de leitura ou implementar consultas que combinam dados de mais de uma entidade. | Quando usar projeções, fluxo de consulta com repositório, mapper e presenter, regras e cuidados com cache, e modelo `CommentWithAuthor`. |
| [rules/domain-value-object-examples.md](rules/domain-value-object-examples.md) | Ao precisar de um modelo completo de value object. | Exemplos `FileUrl` com `create()` e `ResourceUrl` com fábricas nomeadas. |
| [rules/domain-checklist.md](rules/domain-checklist.md) | Ao concluir ou revisar entidades e value objects. | Checklist de nomes, imports, identidade, fábricas, getters, setters, projeções e igualdade. |
| [rules/app-layer.md](rules/app-layer.md) | Antes de implementar ou revisar casos de uso, contratos de repositório ou ports. | Formato dos casos de uso, localização dos contratos e estratégia de resultados e erros. |
| [rules/infra-layer.md](rules/infra-layer.md) | Antes de implementar ou revisar repositórios concretos, mappers, schemas ou adapters. | Responsabilidades da infraestrutura do módulo e da infraestrutura compartilhada em `src/infra/`. |
| [rules/infra-roles.md](rules/infra-roles.md) | Antes de implementar ou revisar qualquer recurso técnico ou integração, para decidir qual elemento criar. | Papéis de gateway, contract da infra, adapter, factory, `index.ts` e bootstrap, e o que cada um não deve fazer. |
| [rules/infra-organization.md](rules/infra-organization.md) | Antes de criar pastas ou nomear arquivos em `src/infra/`, `src/gateways/` ou na `infrastructure/` de um módulo. | Árvore de referência dos recursos compartilhados e nomenclatura de contracts, gateways, adapters e factories. |
| [rules/infra-contract-placement.md](rules/infra-contract-placement.md) | Antes de criar um contrato ou adicionar imports entre aplicação, gateways e infraestrutura. | Os três tipos de contrato (persistência, integração e recurso técnico), onde cada um fica e os imports permitidos. |
| [rules/infra-technical-contracts.md](rules/infra-technical-contracts.md) | Antes de definir o contrato de um recurso técnico, como banco de dados ou servidor HTTP. | Modelos de `DatabaseContract` e `HttpContract`, ciclo de vida e preservação dos tipos concretos. |
| [rules/infra-gateways.md](rules/infra-gateways.md) | Antes de criar ou revisar um gateway ou o contrato técnico de uma integração. | Modelo de `SecretsGateway`, tratamento de ausência e falhas e extensão por contrato técnico. |
| [rules/infra-adapters.md](rules/infra-adapters.md) | Antes de implementar ou revisar um adapter de provedor, driver, SDK ou framework. | Regras de encapsulamento, injeção e tradução de respostas e falhas, com modelo de adapter. |
| [rules/infra-factories.md](rules/infra-factories.md) | Antes de criar factories de infraestrutura, expor um `index.ts` ou decidir escopo de instâncias. | Modelo de factory, composição, efeitos na importação, singletons e reutilização de pools e clientes. |
| [rules/infra-lifecycle.md](rules/infra-lifecycle.md) | Antes de implementar ou alterar bootstrap, inicialização ou encerramento de recursos. | Ordem de inicialização e encerramento, limpeza após falhas e chamadas repetidas de `start()` e `stop()`. |
| [rules/infra-security.md](rules/infra-security.md) | Antes de lidar com ambiente, segredos, timeouts, retries, cache de segredos, TLS ou criptografia. | Regras de configuração, tratamento de erros e segurança das integrações. |
| [rules/http-layer.md](rules/http-layer.md) | Antes de implementar ou revisar controllers, schemas de validação, presenters ou factories de casos de uso. | Responsabilidades da apresentação e a regra de schemas no arquivo do controller. |
| [rules/test-organization.md](rules/test-organization.md) | Antes de criar ou revisar testes. | Localização dos specs, suporte em `test/`, limites dos testes unitários e de integração e a regra de testes E2E para controllers. |
| [rules/test-domain.md](rules/test-domain.md) | Antes de criar ou revisar testes de entidades ou value objects. | Regra de não testar entidades e aggregate roots e o que cobrir em value objects. |
| [rules/test-infrastructure.md](rules/test-infrastructure.md) | Antes de testar ou revisar casos de uso com gateways, adapters, factories ou ciclo de vida. | O que testar em cada elemento da infraestrutura e checklist de revisão. |

- Quando a tarefa envolver mais de um arquivo, consulte as seções relevantes de cada um; não carregue todos os exemplos para uma alteração que não os utiliza.
- Trate as regras como referência de implementação, não como justificativa para ampliar o escopo. Exemplos em TypeScript devem ser adaptados à stack e aos contratos reais do projeto.
- Respeite as instruções específicas do projeto. Preserve convenções existentes quando a tarefa não exigir alterá-las; não substitua primitivas ou estratégias de erro apenas para reproduzir um exemplo.

## Antes de implementar

1. Leia as instruções do projeto, como `AGENTS.md`.
2. Identifique stack, dependências, comandos de validação e convenções existentes.
3. Examine um fluxo semelhante, identifique o módulo responsável e consulte as regras aplicáveis.
4. Defina o comportamento esperado, os cenários de sucesso e erro e as camadas afetadas.
5. Em projetos novos, crie somente a estrutura necessária. Em projetos existentes, evite renomeações, movimentações e abstrações fora do escopo.

## Regras essenciais

Considere as setas como imports permitidos, não como fluxo de execução:

```text
application    → domain → core
infrastructure → application, domain, core e src/infra
presentation   → application, domain, core e src/infra/http
```

- Mantenha domínio e aplicação livres de ORM, framework HTTP, ambiente e SDKs externos, inclusive nos imports de tipos. `core/` não importa módulos de negócio ou infraestrutura.
- Coloque invariantes e comportamentos de negócio no domínio. Casos de uso coordenam entidades e dependências por contratos, com entrada e saída explícitas e sem detalhes de transporte ou persistência.
- Injete implementações concretas nos pontos de composição, como factories e bootstrap; não em entidades ou casos de uso.
- Organize módulos por capacidade de negócio. Comunique-os por contratos públicos de aplicação ou eventos, nunca por repositórios concretos, schemas ou detalhes internos, mesmo compartilhando o banco.
- Resolva configuração de ambiente na infraestrutura e injete os valores necessários; não versione segredos.

Estas regras valem para qualquer tarefa. Os detalhes de cada camada, de entidades, value objects, projeções, controllers e infraestrutura estão nos arquivos de `rules/`; consulte-os pelo índice acima antes de implementar.

## Fluxo de implementação

1. Crie ou ajuste entidades, value objects e contratos necessários, reutilizando os existentes.
2. Implemente o caso de uso com a estratégia de resultados e erros já adotada; teste-o com dependências em memória ou fakes.
3. Implemente persistência, mappers e adapters necessários.
4. Conecte as dependências na composição e implemente controller, validação e presenter.
5. Registre controllers, schemas e integrações no mecanismo existente.
6. Execute as validações relevantes e revise dependências entre camadas e módulos.

Adapte a sequência ao escopo: uma correção pontual não exige recriar o fluxo completo.

## Validação e revisão

- Siga a organização de testes do projeto; mantenha testes unitários de domínio e aplicação independentes de banco, rede e servidor HTTP.
- Antes de criar ou revisar testes, consulte as regras `rules/test-*.md`: elas definem o que testar em cada camada, incluindo a ausência de testes para entidades e os testes E2E de controllers.
- Teste persistência e integrações com dependências controladas.
- Execute testes, checagem de tipos e lint pertinentes com os comandos existentes. Não afirme que passaram sem executá-los.
- Em auditorias, apresente caminhos e evidências. Diferencie violações de dependência de preferências de nomenclatura e não trate pastas opcionais ausentes como falhas.
- Ao concluir, resuma alterações e validações realizadas, indicando o que não foi possível validar.
