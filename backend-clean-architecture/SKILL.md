---
name: backend-clean-architecture
description: Crie, revise ou refatore backends organizados como Modular Monolith com Clean Architecture. Use ao estruturar módulos de negócio, implementar entidades, casos de uso, contratos, repositórios, adapters, controllers e testes, ou auditar dependências entre camadas e isolamento entre módulos. Consulte os guidelines complementares conforme a tarefa e adapte as instruções à stack e às convenções do projeto.
---

# Backend: Modular Monolith com Clean Architecture

## Escopo

Aplique esta skill ao implementar funcionalidades, revisar fronteiras ou refatorar backends nessa arquitetura. Não imponha uma migração arquitetural ao corrigir uma tarefa pontual em um projeto com outra organização.

## Guidelines sob demanda

Os detalhes e exemplos ficam nos arquivos ao lado desta skill. Resolva os caminhos abaixo a partir da pasta que contém este `SKILL.md`, não da raiz do projeto de destino. Consulte apenas os guias e seções relevantes à tarefa:

| Arquivo | Quando consultar | Conteúdo |
| --- | --- | --- |
| [STRUCTURE.md](STRUCTURE.md) | Antes de criar ou reorganizar módulos, distribuir responsabilidades entre camadas, implementar casos de uso, persistência ou controllers, ou auditar a arquitetura. | Árvore de referência, responsabilidades, contratos, composição, isolamento entre módulos e organização dos testes. |
| [ENTITIES.md](ENTITIES.md) | Antes de criar, alterar ou revisar entidades, aggregate roots, value objects, primitivas do domínio ou mapeamentos de identificadores. | Identidade principal sempre `UniqueEntityID` numérico; propriedades adicionais de ID público, como `public_id`, usam `PublicEntityID` UUID quando previstas no modelo. Propriedades, herança, fábricas, datas, getters, setters e exemplos. |
| [INFRASTRUCTURE.md](INFRASTRUCTURE.md) | Antes de implementar ou revisar recursos técnicos, gateways, contracts, adapters, factories, pontos de acesso ou ciclo de vida da infraestrutura. | Organização e direção dos imports, distinção entre gateways e contratos técnicos, exemplos, composição, configuração, segurança e testes. |

- Quando a tarefa envolver mais de um guia, consulte as seções relevantes de cada um; não carregue todos os exemplos para uma alteração que não os utiliza.
- Trate os guias como referência de implementação, não como justificativa para ampliar o escopo. Exemplos em TypeScript devem ser adaptados à stack e aos contratos reais do projeto.
- Respeite as instruções específicas do projeto. Preserve convenções existentes quando a tarefa não exigir alterá-las; não substitua primitivas ou estratégias de erro apenas para reproduzir um exemplo.

## Antes de implementar

1. Leia as instruções do projeto, como `AGENTS.md`.
2. Identifique stack, dependências, comandos de validação e convenções existentes.
3. Examine um fluxo semelhante, identifique o módulo responsável e consulte os guidelines aplicáveis.
4. Defina o comportamento esperado, os cenários de sucesso e erro e as camadas afetadas.
5. Em projetos novos, crie somente a estrutura necessária. Em projetos existentes, evite renomeações, movimentações e abstrações fora do escopo.

## Regras essenciais

Considere as setas como imports permitidos, não como fluxo de execução:

```text
application    → domain → core
infrastructure → application, domain e core
presentation   → application, domain e core
```

- Mantenha domínio e aplicação livres de ORM, framework HTTP, ambiente e SDKs externos, inclusive nos imports de tipos. `core/` não importa módulos de negócio ou infraestrutura.
- Coloque invariantes e comportamentos de negócio no domínio. Casos de uso coordenam entidades e dependências por contratos, com entrada e saída explícitas e sem detalhes de transporte ou persistência.
- Injete implementações concretas nos pontos de composição, como factories e bootstrap; não em entidades ou casos de uso.
- Faça mappers converterem persistência e domínio. Resolva configuração de ambiente na infraestrutura e injete os valores necessários; não versione segredos.
- Faça controllers validarem a entrada, chamarem casos de uso e traduzirem resultados para o transporte. Mantenha seus schemas no próprio arquivo e valide-os pelo fluxo HTTP nos testes E2E, sem duplicar suas definições.
- Use presenters para formatar respostas públicas sem consultas, regras de negócio ou exposição de credenciais e dados sensíveis.
- Organize módulos por capacidade de negócio. Comunique-os por contratos públicos de aplicação ou eventos, nunca por repositórios concretos, schemas ou detalhes internos, mesmo compartilhando o banco.
- Compartilhe apenas conceitos realmente comuns; não mova regras de negócio para `utils/` nem para `core/` apenas para facilitar imports.
- Não force organização de schemas e migrations incompatível com a ferramenta. Não crie diretórios vazios nem introduza CQRS, eventos ou mensageria sem necessidade concreta.

## Fluxo de implementação

1. Crie ou ajuste entidades, value objects e contratos necessários, reutilizando os existentes.
2. Implemente o caso de uso com a estratégia de resultados e erros já adotada; teste-o com dependências em memória ou fakes.
3. Implemente persistência, mappers e adapters necessários.
4. Conecte as dependências na composição e implemente controller, validação e presenter.
5. Registre rotas, schemas e integrações no mecanismo existente.
6. Execute as validações relevantes e revise dependências entre camadas e módulos.

Adapte a sequência ao escopo: uma correção pontual não exige recriar o fluxo completo.

## Validação e revisão

- Siga a organização de testes do projeto; mantenha testes unitários de domínio e aplicação independentes de banco, rede e servidor HTTP.
- Não crie testes para entidades ou aggregate roots. Entre entidades e value objects, teste apenas value objects, conforme `ENTITIES.md`; mantenha os testes das demais camadas conforme suas regras específicas.
- Teste persistência e integrações com dependências controladas.
- Controllers devem ter apenas testes E2E, exercitando as rotas HTTP da aplicação e cobrindo validação, tradução de erros e formato das respostas. Não crie testes unitários ou de integração isolados para controllers ou seus schemas.
- Execute testes, checagem de tipos e lint pertinentes com os comandos existentes. Não afirme que passaram sem executá-los.
- Em auditorias, apresente caminhos e evidências. Diferencie violações de dependência de preferências de nomenclatura e não trate pastas opcionais ausentes como falhas.
- Ao concluir, resuma alterações e validações realizadas, indicando o que não foi possível validar.
