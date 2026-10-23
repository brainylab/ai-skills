# Padrão de entidades e value objects

## Objetivo

Defina entidades e value objects com uma estrutura consistente e reutilizável entre projetos: propriedades tipadas, classes-base compartilhadas, getters, setters quando necessários e fábricas estáticas.

Os exemplos são modelos estruturais com nomes ilustrativos. Substitua nomes, propriedades e valores padrão pelo conceito de negócio a implementar, sem acrescentar comportamentos não solicitados.

Antes de aplicar o padrão, confira os contratos de `Entity`, `AggregateRoot`, `ValueObject`, `DefaultProps`, `UniqueEntityID` e `Optional` disponíveis no projeto, além de `PublicEntityID` quando houver propriedades de ID público. A quantidade de parâmetros genéricos e as assinaturas de construção podem variar, mas a identidade principal da entidade é sempre `UniqueEntityID`. Este documento não exige substituir essas implementações.

## 1. Organização e nomenclatura

- Coloque entidades em `src/modules/<modulo>/domain/entities/`.
- Coloque value objects em `src/modules/<modulo>/domain/entities/value-objects/`.
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

## 7. Estrutura dos value objects

- Estenda `ValueObject<Props>`.
- Declare as propriedades no mesmo arquivo da classe.
- Utilize o construtor protegido herdado e exponha dados ou resultados por getters.
- Construa a instância por `static create(props)` ou por fábricas estáticas nomeadas.
- Não receba uma identidade separada no construtor da base de value objects.
- As propriedades podem conter valores simples, entidades, outros value objects e coleções, conforme o contrato necessário.
- Quando o objeto incluir datas, seu contrato pode estender `DefaultProps` e expor essas datas por getters. Não imponha esse contrato a todos os value objects.

Não acrescente setters aos value objects apenas para reproduzir a estrutura de uma entidade. Também não presuma que getters e ausência de setters garantam imutabilidade profunda: a proteção efetiva depende da implementação da base e dos valores expostos.

### Construção

- Use `create()` para receber as propriedades e retornar a instância.
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

Quando a base comparar propriedades com `JSON.stringify()`, reconheça que esse é o comportamento daquela implementação, não uma garantia de igualdade semântica para qualquer tipo de propriedade. Confira o contrato antes de depender dessa comparação em um novo conceito.

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

## 10. Checklist de aplicação

- Os nomes das classes, propriedades, arquivos e diretórios representam o conceito implementado, não os nomes ilustrativos deste guia.
- Os imports apontam para as primitivas e aliases disponíveis no projeto.
- A assinatura de `AggregateRoot` corresponde à implementação compartilhada.
- A identidade principal é sempre `UniqueEntityID` numérico, mantida pela classe-base e preservada pela fábrica, com incremento sob responsabilidade da persistência.
- `PublicEntityID` representa somente UUIDs em propriedades adicionais previstas no modelo, nunca a identidade principal ou seu parâmetro genérico.
- A presença de `public_id` não é obrigatória em todas as entidades; quando existir, a fábrica e os mappers preservam seu valor sem gerar outro UUID na reconstituição.
- Os valores padrão utilizam `??` e não sobrescrevem valores explícitos válidos.
- Os getters expõem o estado necessário e os setters existem somente onde há alteração prevista.
- Setters atualizam as propriedades, as datas específicas quando existentes e `updatedAt` por `touch()`.
- Os value objects utilizam `create()` ou fábricas nomeadas conforme a forma de construção necessária.
- Não foram acrescentados métodos, validações, erros, eventos, políticas de cópia ou estratégias de igualdade sem requisito explícito.
