# Testes de entidades e value objects

Não crie testes para entidades ou aggregate roots. Entre entidades e value objects, crie testes apenas para value objects, cobrindo os comportamentos previstos no contrato.

Para valores como `Slug`, cubra a transformação de texto e a preservação do valor na reconstituição. Para objetos compostos, cubra fábricas, getters e a igualdade disponibilizada pela base, sem inventar garantias de imutabilidade ou comparação semântica. Não escreva testes de `equals()` para classes que não oferecem esse método. Preserve testes existentes; adicione novos testes quando necessário, sem alterar os já escritos.
