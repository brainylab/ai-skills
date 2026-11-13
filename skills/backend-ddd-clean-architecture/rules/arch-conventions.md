# General conventions

- Use `kebab-case` file names, and names that express the responsibility of each unit.
- Keep the spellings `infrastructure` and `presentation`; in this model, `infra/` is the abbreviation reserved for shared infrastructure.
- Do not put business rules in `utils/`. Utilities used by the inner layers must stay free of technical dependencies.
- Centralize environment variable validation and do not commit secrets.
- Split `commands/` and `queries/` or introduce messaging only when the complexity justifies it.
- Prioritize clear boundaries and correct dependencies, not the number of directories.
