# Domínio: objetivo, organização e nomenclatura

Defina entidades e value objects com uma estrutura consistente e reutilizável entre projetos: propriedades tipadas, classes-base compartilhadas, getters, setters quando necessários e fábricas estáticas.

Os exemplos são modelos estruturais com nomes ilustrativos. Substitua nomes, propriedades e valores padrão pelo conceito de negócio a implementar, sem acrescentar comportamentos não solicitados.

Antes de aplicar o padrão, confira os contratos de `Entity`, `AggregateRoot`, `ValueObject`, `DefaultProps`, `UniqueEntityID` e `Optional` disponíveis no projeto, além de `PublicEntityID` quando houver propriedades de ID público. A quantidade de parâmetros genéricos e as assinaturas de construção podem variar, mas a identidade principal da entidade é sempre `UniqueEntityID`. Este documento não exige substituir essas implementações.

## Organização e nomenclatura

- Coloque entidades em `src/modules/<modulo>/domain/entities/`.
- Coloque value objects em `src/modules/<modulo>/domain/entities/value-objects/`.
- Em projetos existentes, preserve a organização equivalente. Por exemplo, um projeto pode utilizar `src/domain/<contexto>/enterprise/entities/` e seu subdiretório `value-objects/`; não mova esses arquivos apenas para reproduzir a árvore de referência.
- Mantenha as primitivas compartilhadas em `src/core/entities/` e o tipo `Optional` em `src/core/types/`.
- Use arquivos em `kebab-case` e classes em `PascalCase`.
- Declare `<Nome>Props` no mesmo arquivo da classe. Exporte a interface quando ela fizer parte do contrato consumido por outros arquivos.
- Preserve a convenção de nomes das propriedades adotada pelo projeto, sem converter automaticamente entre `camelCase` e `snake_case`.
- Ajuste os aliases de importação ao projeto. Os exemplos destas regras utilizam `@core/...` apenas como referência.
- Use `import type` para dependências utilizadas somente como tipos. Classes instanciadas no arquivo precisam de import de valor.
