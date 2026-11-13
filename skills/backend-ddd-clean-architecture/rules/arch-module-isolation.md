# Isolamento entre módulos

- Organize módulos por capacidade de negócio, como `users`, `orders` e `billing`, não por tabela do banco.
- Um módulo não acessa repositórios concretos, schemas ou detalhes internos de outro módulo.
- Quando houver comunicação entre módulos, exponha contratos de aplicação ou eventos com dados explícitos.
- Compartilhar o mesmo banco não elimina as fronteiras entre módulos.
- Use `core/` apenas para conceitos realmente compartilhados. Não mova regras específicas de um módulo para lá para facilitar imports.
- Contratos de integrações usados por vários módulos podem ficar em `gateways/`; contratos exclusivos permanecem no módulo consumidor.
