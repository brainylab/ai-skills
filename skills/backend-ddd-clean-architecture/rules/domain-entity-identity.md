# Domínio: identidade e assinatura da base

## Identidade principal: sempre `UniqueEntityID`

- O `id` principal de toda entidade é sempre `UniqueEntityID`, encapsulando um `number` associado ao identificador autoincremental da persistência. Não aceite UUIDs ou strings nessa primitiva.
- Mantenha o `id` na classe-base, fora da interface de propriedades. Receba-o como segundo argumento de `create()` e encaminhe-o ao construtor sem substituí-lo.
- O incremento é responsabilidade da persistência, não da entidade ou do wrapper de identidade. Não use `new UniqueEntityID()` sem valor nem gere números aleatórios ou sequenciais no domínio.
- Se o ID só existir após a inserção, siga o contrato do projeto para entidades ainda não persistidas, sem trocar seu tipo por `PublicEntityID` nem inventar IDs provisórios.

## Propriedade adicional de ID público: `PublicEntityID`

- Use `PublicEntityID`, exclusivamente UUID, somente quando o modelo possuir uma propriedade adicional de ID público, como `public_id`. Essa propriedade não substitui nem altera o `id` principal.
- Declare a propriedade em `<Nome>Props` e exponha-a por getter, como os demais dados da entidade. Preserve a convenção de nomes do projeto.
- A existência do tipo `PublicEntityID` não exige adicionar `public_id` a todas as entidades. Quando o modelo exigir o campo, ele pode ser obrigatório nas propriedades; opcional é sua existência no modelo, não necessariamente seu valor.
- Não use `PublicEntityID` como argumento de identidade da classe-base nem como tipo genérico da identidade principal.
- Preserve UUIDs recebidos. Gere um novo UUID somente quando previsto para a criação, seguindo a API da primitiva; nunca ao reconstituir uma entidade existente.
- Mappers devem preservar o `id` principal e a propriedade pública, quando existente, separadamente. O uso dessa propriedade nos contratos externos depende dos requisitos do projeto e não substitui autorização.

## Parâmetros genéricos

As duas formas abaixo são alternativas, não assinaturas intercambiáveis:

- `AggregateRoot<ConfigurationProps>`: para uma base que recebe somente o contrato de propriedades.
- `AggregateRoot<ConfigurationProps, UniqueEntityID>`: para uma base que também recebe o tipo da identidade.

Escolha a forma suportada pelo projeto. Não acrescente um parâmetro genérico a uma base que não o declara.
