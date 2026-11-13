# AI Skills

Skills para agentes de código com as diretrizes de arquitetura da BrainyLab.

## Skills disponíveis

| Skill | Descrição |
| --- | --- |
| [backend-ddd-clean-architecture](skills/backend-ddd-clean-architecture/SKILL.md) | Backends em TypeScript organizados como Modular Monolith com DDD e Clean Architecture: estrutura de módulos, regra de dependência, entidades, value objects, casos de uso, infraestrutura, gateways, controllers e testes. |

## Instalação

Com a CLI do [skills.sh](https://skills.sh):

```sh
npx skills add brainylab/ai-skills
```

Para instalar uma skill específica:

```sh
npx skills add brainylab/ai-skills --skill backend-ddd-clean-architecture
```

A CLI detecta os agentes instalados (Claude Code, Zed, Cursor e outros) e copia a skill para a pasta de cada um.

## Como a skill está organizada

```text
skills/backend-ddd-clean-architecture/
├── SKILL.md      # escopo, índice das regras, regras essenciais e fluxo de trabalho
└── rules/        # uma regra por arquivo, lida sob demanda
```

O `SKILL.md` é carregado quando a skill é acionada. Os arquivos de `rules/` são consultados conforme a tarefa, e o prefixo do nome indica a área:

| Prefixo | Área |
| --- | --- |
| `arch-` | Arquitetura geral: estrutura, dependências, módulos e convenções |
| `domain-` | Entidades, value objects e projeções de leitura |
| `app-` | Casos de uso e contratos |
| `infra-` | Infraestrutura, gateways, adapters e factories |
| `http-` | Controllers e presenters |
| `test-` | Testes |

Os exemplos são em TypeScript e servem de modelo estrutural: a skill orienta o agente a adaptar nomes, aliases e contratos às convenções do projeto em que estiver trabalhando.

## Idioma

As regras estão em português.
