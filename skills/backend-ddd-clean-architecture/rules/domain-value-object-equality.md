# Domínio: igualdade de value objects

Utilize o contrato de `equals()` disponível em `ValueObject`. Não acrescente uma implementação alternativa apenas para seguir esta regra.

Na base de referência, `equals()` retorna `false` para argumento nulo, indefinido ou sem `props`, e compara `JSON.stringify(vo.props)` com `JSON.stringify(this.props)`. Portanto, instâncias distintas podem ser iguais quando suas propriedades produzem a mesma serialização. IDs presentes nas propriedades participam dessa comparação como dados; não são uma identidade separada da base.

Essa estratégia não garante igualdade semântica para qualquer tipo:

- A ordem das chaves pode afetar o resultado e propriedades com `undefined` podem ser omitidas.
- Datas são comparadas por sua representação serializada, não pela identidade das instâncias de `Date`.
- A comparação não verifica a classe concreta e não chama recursivamente os métodos `equals()` de IDs, entidades ou outros value objects.
- Referências circulares não são suportadas por `JSON.stringify()`.

Confira o contrato antes de depender dessa comparação em um novo conceito. Não estenda essas garantias a classes independentes: um `Slug` implementado sem herdar `ValueObject`, por exemplo, não disponibiliza `equals()`.
