# Domínio: estrutura e funcionamento dos value objects

## Conceito: entidade versus valor

Uma entidade representa algo cuja continuidade é acompanhada por uma identidade, mesmo quando seus dados mudam. Um value object representa um valor ou uma composição de dados; sua interpretação depende do conteúdo, não de uma identidade própria e de um ciclo de vida independente.

- Use entidades e aggregate roots para estado e comportamentos de negócio acompanhados por identidade.
- Use value objects para encapsular conceitos, normalizações, cálculos ou composições de dados que não precisam dessa identidade própria.
- Um value object pode carregar IDs de entidades como parte de suas propriedades. Isso não o transforma em entidade: esses IDs referenciam a origem dos dados, não uma identidade própria gerenciada por `Entity`.
- Value objects também podem ser usados como projeções de leitura, conforme [domain-read-projections.md](domain-read-projections.md). Esse é um padrão opcional, não uma obrigação de modelar todo resultado de consulta como value object.

## Estrutura

- Para novos objetos compostos que sigam a base compartilhada, estenda `ValueObject<Props>`. Antes de alterar um objeto existente, confira se ele utiliza essa base ou uma implementação independente.
- Declare as propriedades no mesmo arquivo da classe.
- Utilize o construtor protegido herdado e exponha dados ou resultados por getters.
- Construa a instância por `static create(props)` ou por fábricas estáticas nomeadas.
- Não receba uma identidade separada no construtor da base de value objects.
- As propriedades podem conter valores simples, entidades, outros value objects e coleções, conforme o contrato necessário.
- Quando o objeto incluir datas, seu contrato pode estender `DefaultProps` e expor essas datas por getters. Não imponha esse contrato a todos os value objects.

Não acrescente setters, `touch()` ou eventos de domínio aos value objects apenas para reproduzir a estrutura de uma entidade. Para representar outro valor ou atualizar uma projeção, prefira construir uma nova instância.

Getters e ausência de setters não garantem imutabilidade profunda. Na base de referência, `protected props` restringe acesso no TypeScript, mas não congela nem copia as propriedades. Objetos, datas e arrays recebidos ou retornados por getters continuam compartilhando referências. Não afirme que o objeto é imutável em runtime nem introduza cópias defensivas ou congelamento sem um requisito explícito.

## Construção

- Use `create()` para receber as propriedades e retornar a instância.
- Diferencie criação a partir de entrada bruta de reconstituição de um valor já armazenado. Uma fábrica de normalização pode transformar o texto; uma fábrica de reconstituição deve preservar o valor conforme seu contrato.
- A presença de `create()` não significa que exista validação. Confira o corpo da fábrica antes de atribuir a ela garantias de validade.
- Use fábricas nomeadas quando a construção selecionar um contexto ou valor interno.
- Separe `<Nome>Params` de `<Nome>Props` quando a fábrica receber parte dos dados e acrescentar propriedades antes de chamar o construtor.
- Não introduza validações, erros ou conversões que não façam parte do contrato solicitado.

## Getters e cálculos

- Utilize `value` quando o objeto expuser um resultado principal, como uma URL montada a partir das propriedades.
- Para objetos compostos, exponha getters com os nomes dos dados ou resultados correspondentes.
- Métodos de cálculo podem ser utilizados pelos getters para produzir valores derivados das propriedades.
- Receba explicitamente os dados necessários, como uma URL base, sem depender de configuração global dentro do objeto.
