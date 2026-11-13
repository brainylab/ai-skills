# AI Skills

Skills for coding agents with BrainyLab's architecture guidelines.

## Available skills

| Skill | Description |
| --- | --- |
| [backend-ddd-clean-architecture](skills/backend-ddd-clean-architecture/SKILL.md) | TypeScript backends organized as a Modular Monolith with DDD and Clean Architecture: module structure, dependency rule, entities, value objects, use cases, infrastructure, gateways, controllers and tests. |

## Installation

With the [skills.sh](https://skills.sh) CLI:

```sh
npx skills add brainylab/ai-skills
```

To install a specific skill:

```sh
npx skills add brainylab/ai-skills --skill backend-ddd-clean-architecture
```

The CLI detects the installed agents (Claude Code, Zed, Cursor and others) and copies the skill into each one's folder.

## How the skill is organized

```text
skills/backend-ddd-clean-architecture/
├── SKILL.md      # scope, index of the rules, essential rules and workflow
└── rules/        # one rule per file, read on demand
```

`SKILL.md` is loaded when the skill is triggered. The files in `rules/` are consulted according to the task, and the file name prefix indicates the area:

| Prefix | Area |
| --- | --- |
| `arch-` | General architecture: structure, dependencies, modules and conventions |
| `domain-` | Entities, value objects and read projections |
| `app-` | Use cases and contracts |
| `infra-` | Infrastructure, gateways, adapters and factories |
| `http-` | Controllers and presenters |
| `test-` | Tests |

The examples are in TypeScript and serve as structural models: the skill instructs the agent to adapt names, aliases and contracts to the conventions of the project it is working on.
