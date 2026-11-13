# Domínio: fábrica estática `create()`

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

## Datas na construção

- Preserve datas recebidas nas propriedades.
- Quando a fábrica definir `createdAt`, utilize `props.createdAt ?? new Date()`.
- Quando as datas forem inicializadas pela classe-base, respeite esse contrato.
- Datas específicas do negócio podem continuar opcionais até que um setter as atualize.
- A atribuição de valores a `props` na construção não executa os setters da entidade.
