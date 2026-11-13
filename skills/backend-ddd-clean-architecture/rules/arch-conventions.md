# Convenções gerais

- Use nomes de arquivos em `kebab-case` e nomes que expressem a responsabilidade de cada unidade.
- Mantenha as grafias `infrastructure` e `presentation`; neste modelo, `infra/` é a abreviação reservada à infraestrutura compartilhada.
- Não coloque regras de negócio em `utils/`. Utilitários das camadas internas devem continuar livres de dependências técnicas.
- Centralize a validação de variáveis de ambiente e não versione segredos.
- Separe `commands/` e `queries/` ou introduza mensageria somente quando a complexidade justificar.
- Priorize fronteiras claras e dependências corretas, não a quantidade de diretórios.
