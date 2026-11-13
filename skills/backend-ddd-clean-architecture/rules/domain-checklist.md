# Domínio: checklist de entidades e value objects

- Os nomes das classes, propriedades, arquivos e diretórios representam o conceito implementado, não os nomes ilustrativos destas regras.
- Os imports apontam para as primitivas e aliases disponíveis no projeto.
- A assinatura de `AggregateRoot` corresponde à implementação compartilhada.
- A identidade principal é sempre `UniqueEntityID` numérico, mantida pela classe-base e preservada pela fábrica, com incremento sob responsabilidade da persistência.
- `PublicEntityID` representa somente UUIDs em propriedades adicionais previstas no modelo, nunca a identidade principal ou seu parâmetro genérico.
- A presença de `public_id` não é obrigatória em todas as entidades; quando existir, a fábrica e os mappers preservam seu valor sem gerar outro UUID na reconstituição.
- Os valores padrão utilizam `??` e não sobrescrevem valores explícitos válidos.
- Os getters expõem o estado necessário e os setters existem somente onde há alteração prevista.
- Setters atualizam as propriedades, as datas específicas quando existentes e `updatedAt` por `touch()`.
- Os value objects utilizam `create()` ou fábricas nomeadas conforme a forma de construção necessária, distinguindo normalização de entrada e reconstituição.
- A escolha entre entidade, valor simples e projeção de leitura segue a responsabilidade do objeto; IDs nas propriedades não implicam identidade própria.
- Projeções não incorporam ORM, consultas ou transporte HTTP e não substituem agregados nas operações de alteração.
- A igualdade e a mutabilidade documentadas correspondem à implementação real, inclusive quando o objeto não herda `ValueObject`.
- Coleções de leitura não recebem rastreamento de alterações apenas por existirem no agregado correspondente.
- Não foram acrescentados métodos, validações, erros, eventos, políticas de cópia ou estratégias de igualdade sem requisito explícito.
