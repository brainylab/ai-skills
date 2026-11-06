# Padrão de entidades e value objects

## Objetivo

Defina entidades e value objects com uma estrutura consistente e reutilizável entre projetos: propriedades tipadas, classes-base compartilhadas, getters, setters quando necessários e fábricas estáticas.

Os exemplos são modelos estruturais com nomes ilustrativos. Substitua nomes, propriedades e valores padrão pelo conceito de negócio a implementar, sem acrescentar comportamentos não solicitados.

Antes de aplicar o padrão, confira os contratos de `Entity`, `AggregateRoot`, `ValueObject`, `DefaultProps`, `UniqueEntityID` e `Optional` disponíveis no projeto, além de `PublicEntityID` quando houver propriedades de ID público. A quantidade de parâmetros genéricos e as assinaturas de construção podem variar, mas a identidade principal da entidade é sempre `UniqueEntityID`. Este documento não exige substituir essas implementações.

## 1. Organização e nomenclatura

- Coloque entidades em `src/modules/<modulo>/domain/entities/`.
- Coloque value objects em `src/modules/<modulo>/domain/entities/value-objects/`.
- Em projetos existentes, preserve a organização equivalente. Por exemplo, `05-nest-clean` utiliza `src/domain/forum/enterprise/entities/` e seu subdiretório `value-objects/`; não mova esses arquivos apenas para reproduzir a árvore deste guia.
- Mantenha as primitivas compartilhadas em `src/core/entities/` e o tipo `Optional` em `src/core/types/`.
- Use arquivos em `kebab-case` e classes em `PascalCase`.
- Declare `<Nome>Props` no mesmo arquivo da classe. Exporte a interface quando ela fizer parte do contrato consumido por outros arquivos.
- Preserve a convenção de nomes das propriedades adotada pelo projeto, sem converter automaticamente entre `camelCase` e `snake_case`.
- Ajuste os aliases de importação ao projeto. Os exemplos deste guia utilizam `@core/...` apenas como referência.
- Use `import type` para dependências utilizadas somente como tipos. Classes instanciadas no arquivo precisam de import de valor.

## 2. Estrutura das entidades

- Declare a interface de propriedades estendendo `DefaultProps`.
- Estenda `AggregateRoot<Props>` ou `AggregateRoot<Props, UniqueEntityID>`, conforme a assinatura da base adotada. O tipo da identidade principal é sempre `UniqueEntityID`, nunca `PublicEntityID`.
- Utilize `this.props` para acessar o estado protegido da entidade.
- Utilize a identidade, as datas e o método `touch()` mantidos pela classe-base, sem duplicar esses membros na entidade concreta.
- Não declare um construtor próprio apenas para repetir o construtor protegido herdado.

O contrato de datas utilizado pelo padrão é:

```ts
export interface DefaultProps {
  createdAt?: Date | null;
  updatedAt?: Date | null;
}
```

`Entity` concentra identidade, propriedades e datas. `AggregateRoot` estende essa estrutura com suporte a eventos de domínio, quando disponibilizado pela implementação compartilhada. A criação de uma entidade não exige adicionar eventos à fábrica.

## 3. Identidade e assinatura da base

### Identidade principal: sempre `UniqueEntityID`

- O `id` principal de toda entidade é sempre `UniqueEntityID`, encapsulando um `number` associado ao identificador autoincremental da persistência. Não aceite UUIDs ou strings nessa primitiva.
- Mantenha o `id` na classe-base, fora da interface de propriedades. Receba-o como segundo argumento de `create()` e encaminhe-o ao construtor sem substituí-lo.
- O incremento é responsabilidade da persistência, não da entidade ou do wrapper de identidade. Não use `new UniqueEntityID()` sem valor nem gere números aleatórios ou sequenciais no domínio.
- Se o ID só existir após a inserção, siga o contrato do projeto para entidades ainda não persistidas, sem trocar seu tipo por `PublicEntityID` nem inventar IDs provisórios.

### Propriedade adicional de ID público: `PublicEntityID`

- Use `PublicEntityID`, exclusivamente UUID, somente quando o modelo possuir uma propriedade adicional de ID público, como `public_id`. Essa propriedade não substitui nem altera o `id` principal.
- Declare a propriedade em `<Nome>Props` e exponha-a por getter, como os demais dados da entidade. Preserve a convenção de nomes do projeto.
- A existência do tipo `PublicEntityID` não exige adicionar `public_id` a todas as entidades. Quando o modelo exigir o campo, ele pode ser obrigatório nas propriedades; opcional é sua existência no modelo, não necessariamente seu valor.
- Não use `PublicEntityID` como argumento de identidade da classe-base nem como tipo genérico da identidade principal.
- Preserve UUIDs recebidos. Gere um novo UUID somente quando previsto para a criação, seguindo a API da primitiva; nunca ao reconstituir uma entidade existente.
- Mappers devem preservar o `id` principal e a propriedade pública, quando existente, separadamente. O uso dessa propriedade nos contratos externos depende dos requisitos do projeto e não substitui autorização.

### Parâmetros genéricos

As duas formas abaixo são alternativas, não assinaturas intercambiáveis:

- `AggregateRoot<ConfigurationProps>`: para uma base que recebe somente o contrato de propriedades.
- `AggregateRoot<ConfigurationProps, UniqueEntityID>`: para uma base que também recebe o tipo da identidade.

Escolha a forma suportada pelo projeto. Não acrescente um parâmetro genérico a uma base que não o declara.

## 4. Fábrica estática `create()`

Siga esta sequência:

1. Receba as propriedades tipadas.
2. Utilize `Optional<Props, Campos>` para indicar os campos que podem ser omitidos na criação.
3. Receba o `id` principal como `UniqueEntityID` no segundo argumento; propriedades adicionais de ID público, quando previstas, pertencem a `props`.
4. Instancie a própria classe, preservando as propriedades com `...props`.
5. Aplique os valores padrão necessários com `??`.
6. Encaminhe a identidade ao construtor.
7. Retorne a instância criada.

`Optional<T, K>` torna opcionais as propriedades selecionadas de `T`. Ele pode incluir datas conforme a convenção de assinatura adotada, mesmo quando elas já forem opcionais em `DefaultProps`.

Utilize `??` para preservar valores como `false` e `0`. Não acrescente validações, normalizações ou novos tipos de erro somente para preencher a fábrica.

### Datas na construção

- Preserve datas recebidas nas propriedades.
- Quando a fábrica definir `createdAt`, utilize `props.createdAt ?? new Date()`.
- Quando as datas forem inicializadas pela classe-base, respeite esse contrato.
- Datas específicas do negócio podem continuar opcionais até que um setter as atualize.
- A atribuição de valores a `props` na construção não executa os setters da entidade.

## 5. Getters e setters

### Getters

Exponha as propriedades por getters que retornam `this.props.<campo>`. Utilize os getters herdados para identidade e datas mantidas pela base.

### Setters

Adicione setters somente aos campos que precisam permitir alteração. A presença de um getter não exige um setter correspondente.

Quando a alteração também registrar uma data específica, mantenha a sequência:

1. Atribua o valor recebido à propriedade.
2. Atualize a data correspondente com `new Date()`.
3. Chame `this.touch()` para atualizar `updatedAt`.

Se não houver uma data específica associada à alteração, não crie um campo apenas para reproduzir essa sequência.

No formato de setter mostrado abaixo, a data é atualizada em toda atribuição, inclusive quando o valor é `false` ou igual ao anterior. Ela registra a atribuição, não necessariamente uma transição de estado. Uma política diferente precisa ser definida pelo requisito de negócio, não presumida pelo guia.

Não substitua setters por métodos adicionais nem acrescente funções privadas de normalização sem uma necessidade explícita.

## 6. Modelo de entidade com getters e setter

O modelo utiliza a assinatura de base com um parâmetro genérico e identidade principal obrigatória do tipo `UniqueEntityID`. `configured` e `configuredAt` demonstram o setter e seus efeitos nas datas. Neste exemplo, o modelo também exige a propriedade adicional `public_id: PublicEntityID`; omita essa propriedade, seu import e seu getter em entidades que não possuam ID público.

```ts
import { AggregateRoot } from "@core/entities/aggregate-root";

import type { DefaultProps } from "@core/entities/entity";
import type { PublicEntityID } from "@core/entities/public-entity-id";
import type { UniqueEntityID } from "@core/entities/unique-entity-id";
import type { Optional } from "@core/types/optional";

export interface ConfigurationProps extends DefaultProps {
  public_id: PublicEntityID;
  configured: boolean;
  configuredAt?: Date;
}

export class Configuration extends AggregateRoot<ConfigurationProps> {
  get public_id() {
    return this.props.public_id;
  }

  get configured() {
    return this.props.configured;
  }

  get configuredAt() {
    return this.props.configuredAt;
  }

  set configured(configured: boolean) {
    this.props.configured = configured;
    this.props.configuredAt = new Date();

    this.touch();
  }

  static create(
    props: Optional<ConfigurationProps, "configured" | "configuredAt">,
    id: UniqueEntityID,
  ) {
    const configuration = new Configuration(
      {
        ...props,
        configured: props.configured ?? false,
      },
      id,
    );

    return configuration;
  }
}
```

A fábrica recebe `id: UniqueEntityID` separadamente de `props.public_id: PublicEntityID`. O `...props` preserva a propriedade pública recebida; ela não é encaminhada no lugar do `id` ao construtor da classe-base. Ajuste os imports às localizações reais das primitivas no projeto.

## 7. Estrutura e funcionamento dos value objects

### Conceito: entidade versus valor

Uma entidade representa algo cuja continuidade é acompanhada por uma identidade, mesmo quando seus dados mudam. Um value object representa um valor ou uma composição de dados; sua interpretação depende do conteúdo, não de uma identidade própria e de um ciclo de vida independente.

- Use entidades e aggregate roots para estado e comportamentos de negócio acompanhados por identidade.
- Use value objects para encapsular conceitos, normalizações, cálculos ou composições de dados que não precisam dessa identidade própria.
- Um value object pode carregar IDs de entidades como parte de suas propriedades. Isso não o transforma em entidade: esses IDs referenciam a origem dos dados, não uma identidade própria gerenciada por `Entity`.
- Neste projeto de referência também existem value objects usados como projeções de leitura. Esse é um padrão adotado pelo projeto, não uma obrigação de modelar todo resultado de consulta como value object.

### Estrutura

- Para novos objetos compostos que sigam a base compartilhada, estenda `ValueObject<Props>`. Antes de alterar um objeto existente, confira se ele utiliza essa base ou uma implementação independente.
- Declare as propriedades no mesmo arquivo da classe.
- Utilize o construtor protegido herdado e exponha dados ou resultados por getters.
- Construa a instância por `static create(props)` ou por fábricas estáticas nomeadas.
- Não receba uma identidade separada no construtor da base de value objects.
- As propriedades podem conter valores simples, entidades, outros value objects e coleções, conforme o contrato necessário.
- Quando o objeto incluir datas, seu contrato pode estender `DefaultProps` e expor essas datas por getters. Não imponha esse contrato a todos os value objects.

Não acrescente setters, `touch()` ou eventos de domínio aos value objects apenas para reproduzir a estrutura de uma entidade. Para representar outro valor ou atualizar uma projeção, prefira construir uma nova instância.

Getters e ausência de setters não garantem imutabilidade profunda. Na base observada, `protected props` restringe acesso no TypeScript, mas não congela nem copia as propriedades. Objetos, datas e arrays recebidos ou retornados por getters continuam compartilhando referências. Não afirme que o objeto é imutável em runtime nem introduza cópias defensivas ou congelamento sem um requisito explícito.

### Construção

- Use `create()` para receber as propriedades e retornar a instância.
- Diferencie criação a partir de entrada bruta de reconstituição de um valor já armazenado. Uma fábrica de normalização pode transformar o texto; uma fábrica de reconstituição deve preservar o valor conforme seu contrato.
- A presença de `create()` não significa que exista validação. Confira o corpo da fábrica antes de atribuir a ela garantias de validade.
- Use fábricas nomeadas quando a construção selecionar um contexto ou valor interno.
- Separe `<Nome>Params` de `<Nome>Props` quando a fábrica receber parte dos dados e acrescentar propriedades antes de chamar o construtor.
- Não introduza validações, erros ou conversões que não façam parte do contrato solicitado.

### Getters e cálculos

- Utilize `value` quando o objeto expuser um resultado principal, como uma URL montada a partir das propriedades.
- Para objetos compostos, exponha getters com os nomes dos dados ou resultados correspondentes.
- Métodos de cálculo podem ser utilizados pelos getters para produzir valores derivados das propriedades.
- Receba explicitamente os dados necessários, como uma URL base, sem depender de configuração global dentro do objeto.

### Igualdade

Utilize o contrato de `equals()` disponível em `ValueObject`. Não acrescente uma implementação alternativa apenas para seguir este guia.

Na base de `05-nest-clean`, `equals()` retorna `false` para argumento nulo, indefinido ou sem `props`, e compara `JSON.stringify(vo.props)` com `JSON.stringify(this.props)`. Portanto, instâncias distintas podem ser iguais quando suas propriedades produzem a mesma serialização. IDs presentes nas propriedades participam dessa comparação como dados; não são uma identidade separada da base.

Essa estratégia não garante igualdade semântica para qualquer tipo:

- A ordem das chaves pode afetar o resultado e propriedades com `undefined` podem ser omitidas.
- Datas são comparadas por sua representação serializada, não pela identidade das instâncias de `Date`.
- A comparação não verifica a classe concreta e não chama recursivamente os métodos `equals()` de IDs, entidades ou outros value objects.
- Referências circulares não são suportadas por `JSON.stringify()`.

Confira o contrato antes de depender dessa comparação em um novo conceito. Não estenda essas garantias a classes independentes: o `Slug` observado, por exemplo, não herda `ValueObject` nem disponibiliza `equals()`.

### Referência observada em `05-nest-clean`

Os arquivos abaixo ficam em `src/domain/forum/enterprise/entities/`, com os value objects em `value-objects/`:

| Objeto | Papel | Construção e uso |
| --- | --- | --- |
| `Question` | Aggregate root com identidade, estado e comportamentos. | Sua fábrica usa `Slug.createFromText(title)` quando não recebe um slug. O setter de `title` substitui o slug e atualiza `updatedAt`. |
| `Slug` | Valor simples que encapsula a representação textual de um slug. | `create(value)` preserva a string recebida; `createFromText(text)` normaliza o texto. Possui construtor privado e `value` público e mutável; não estende a base nem possui igualdade própria. |
| `CommentWithAuthor` | Projeção de leitura que reúne dados do comentário e o nome do autor. | Estende `ValueObject<CommentWithAuthorProps>`, expõe getters e é construído por `create(props)`. `commentId` e `authorId` são referências às entidades consultadas. |
| `QuestionDetails` | Projeção de leitura com dados da pergunta, autor e anexos. | Estende `ValueObject<QuestionDetailsProps>` e reúne IDs, título, conteúdo, `Slug`, `Attachment[]` e datas, sem adquirir o ciclo de vida de `Question`. |

`Slug.createFromText()` aplica normalização Unicode `NFKD`, conversão para minúsculas, remoção de espaços externos, troca de espaços por hífens, remoção de caracteres fora de `\w` e hífen, troca de underscores por hífens, redução de hífens repetidos e remoção do hífen final. Por exemplo, `"Título da pergunta"` se torna `"titulo-da-pergunta"`. Esse processo não valida unicidade nem garante um resultado não vazio. Preserve a sequência real de operações; não descreva a fábrica como um validador genérico de URLs.

O fluxo observado na consulta de comentários é:

1. O contrato do repositório declara o retorno `CommentWithAuthor[]`, sem tipos de Prisma.
2. A implementação na infraestrutura consulta comentários incluindo o autor.
3. `PrismaCommentWithAuthorMapper.toDomain()` converte os dados para `CommentWithAuthor.create(...)`.
4. O caso de uso retorna os value objects e o presenter os converte para o formato HTTP, por exemplo, expondo `author` como `authorName`.

A consulta de detalhes segue a mesma separação: `QuestionsRepository.findDetailsBySlug()` retorna `QuestionDetails | null`, e `PrismaQuestionDetailsMapper` reúne os dados e reconstitui o slug com `Slug.create(raw.slug)`.

Ao aplicar esse padrão:

- Não acrescente o nome do autor a `Question` ou ao comentário apenas para atender uma consulta enriquecida; crie ou reutilize a projeção adequada.
- Mantenha joins, includes e tipos do ORM na infraestrutura. O value object recebe dados explícitos; ele não consulta repositórios, banco ou configuração global.
- Não trate projeção como DTO HTTP: ela é independente do transporte, e o presenter define a resposta pública.
- Não use uma projeção para executar alterações de negócio no lugar da entidade ou do aggregate root.
- Diferencie `QuestionDetails.attachments: Attachment[]`, usado na leitura, de `Question.attachments: QuestionAttachmentList`, que acompanha alterações na coleção do agregado. Não leve rastreamento de alterações para a projeção sem necessidade.
- Ao ler de cache, `JSON.parse()` sozinho não reconstitui instâncias, getters, datas nem wrappers de ID. No caminho de cache observado em `PrismaQuestionsRepository`, o retorno é o objeto parseado diretamente; isso é uma limitação existente, não um padrão a reproduzir.

Os exemplos acima documentam o funcionamento observado, não uma migração de primitivas. `05-nest-clean` utiliza `UniqueEntityID` com strings/UUIDs; essa implementação de referência não altera a política de identidade numérica definida na seção 3 para os projetos que seguem este guia.

## 8. Modelo de value object com `create()`

O modelo demonstra a construção por propriedades e um getter que remove barras finais da URL base e codifica o nome do arquivo. O segmento `files` é ilustrativo; utilize o caminho necessário ao recurso.

```ts
import { ValueObject } from "@core/entities/value-object";

interface FileUrlProps {
  baseUrl: string;
  filename: string;
}

export class FileUrl extends ValueObject<FileUrlProps> {
  get value(): string {
    const baseUrl = this.props.baseUrl.replace(/\/+$/, "");
    const filename = encodeURIComponent(this.props.filename);

    return `${baseUrl}/files/${filename}`;
  }

  static create(props: FileUrlProps) {
    return new FileUrl(props);
  }
}
```

## 9. Modelo de value object com fábricas nomeadas

Neste modelo, os parâmetros de entrada não incluem o diretório: cada fábrica define o contexto. Os nomes das fábricas e os diretórios são ilustrativos e devem representar os contextos necessários ao negócio.

```ts
import { ValueObject } from "@core/entities/value-object";

interface ResourceUrlParams {
  baseUrl: string;
  filename: string;
}

interface ResourceUrlProps extends ResourceUrlParams {
  directory: "images" | "attachments";
}

export class ResourceUrl extends ValueObject<ResourceUrlProps> {
  get value(): string {
    const baseUrl = this.props.baseUrl.replace(/\/+$/, "");
    const filename = encodeURIComponent(this.props.filename);

    return `${baseUrl}/${this.props.directory}/${filename}`;
  }

  static forImage(params: ResourceUrlParams) {
    return new ResourceUrl({ ...params, directory: "images" });
  }

  static forAttachment(params: ResourceUrlParams) {
    return new ResourceUrl({ ...params, directory: "attachments" });
  }
}
```

## 10. Testes

Não crie testes para entidades ou aggregate roots. Entre entidades e value objects, crie testes apenas para value objects, cobrindo os comportamentos previstos no contrato.

Para valores como `Slug`, cubra a transformação de texto e a preservação do valor na reconstituição. Para objetos compostos, cubra fábricas, getters e a igualdade disponibilizada pela base, sem inventar garantias de imutabilidade ou comparação semântica. Não escreva testes de `equals()` para classes que não oferecem esse método. Preserve testes existentes; adicione novos testes quando necessário, sem alterar os já escritos.

## 11. Checklist de aplicação

- Os nomes das classes, propriedades, arquivos e diretórios representam o conceito implementado, não os nomes ilustrativos deste guia.
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
