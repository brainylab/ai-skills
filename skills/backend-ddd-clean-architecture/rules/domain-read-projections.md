# Domínio: projeções de leitura

Uma projeção de leitura é um value object que reúne, para uma consulta, dados de mais de uma entidade — por exemplo, um comentário com o nome do autor. Ela existe para evitar que entidades recebam campos apenas para enriquecer respostas.

Projeções são um padrão opcional. Não modele todo resultado de consulta como value object: use-as quando a consulta precisar de dados que não pertencem a uma única entidade.

## Fluxo de consulta

1. O contrato do repositório, na aplicação, declara a projeção como retorno, sem tipos do ORM.
2. A implementação na infraestrutura executa a consulta com os joins ou includes necessários.
3. Um mapper converte o resultado da persistência na projeção, por sua fábrica `create()`.
4. O caso de uso retorna a projeção e o presenter a converte para o formato público da resposta.

## Regras

- Não acrescente dados de outra entidade a uma entidade ou aggregate root apenas para atender uma consulta enriquecida; crie ou reutilize a projeção adequada.
- Mantenha joins, includes e tipos do ORM na infraestrutura. A projeção recebe dados explícitos; ela não consulta repositórios, banco ou configuração global.
- Não trate a projeção como DTO HTTP: ela é independente do transporte, e o presenter define a resposta pública, inclusive os nomes dos campos expostos.
- Não use uma projeção para executar alterações de negócio no lugar da entidade ou do aggregate root.
- IDs presentes nas propriedades da projeção são referências às entidades consultadas, não uma identidade própria. Eles seguem a política de [domain-entity-identity.md](domain-entity-identity.md).
- Use coleções simples na projeção. Listas com rastreamento de alterações pertencem ao aggregate root; não leve esse rastreamento para a leitura sem necessidade.
- Ao montar a projeção a partir de valores já armazenados, use a fábrica de reconstituição do value object interno, não a de normalização. Um slug persistido, por exemplo, é preservado como está, e não gerado novamente a partir do título.
- Ao ler de cache, `JSON.parse()` sozinho não reconstitui instâncias, getters, datas nem wrappers de ID. Reconstitua a projeção pelo mapper ou pela fábrica antes de retorná-la.

## Modelo

Os nomes são ilustrativos. A projeção reúne dados do comentário e o nome do autor; `commentId` e `authorId` são referências.

```ts
import { ValueObject } from "@core/entities/value-object";

import type { UniqueEntityID } from "@core/entities/unique-entity-id";

export interface CommentWithAuthorProps {
  commentId: UniqueEntityID;
  content: string;
  authorId: UniqueEntityID;
  author: string;
  createdAt: Date;
  updatedAt?: Date | null;
}

export class CommentWithAuthor extends ValueObject<CommentWithAuthorProps> {
  get commentId() {
    return this.props.commentId;
  }

  get content() {
    return this.props.content;
  }

  get authorId() {
    return this.props.authorId;
  }

  get author() {
    return this.props.author;
  }

  get createdAt() {
    return this.props.createdAt;
  }

  get updatedAt() {
    return this.props.updatedAt;
  }

  static create(props: CommentWithAuthorProps) {
    return new CommentWithAuthor(props);
  }
}
```

O contrato do repositório declara, por exemplo, `findManyByQuestionIdWithAuthor(...): Promise<CommentWithAuthor[]>`. O mapper da infraestrutura chama `CommentWithAuthor.create(...)` com os dados da consulta, e o presenter decide como expor `author` na resposta.
