# Domínio: estrutura das entidades

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
