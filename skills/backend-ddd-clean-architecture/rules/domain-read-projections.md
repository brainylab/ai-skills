# Domain: read projections

A read projection is a value object that gathers, for a query, data from more than one entity — for example, a comment together with its author's name. It exists so that entities do not receive fields just to enrich responses.

Projections are an optional pattern. Do not model every query result as a value object: use them when the query needs data that does not belong to a single entity.

## Query flow

1. The repository contract, in the application layer, declares the projection as its return type, without ORM types.
2. The infrastructure implementation runs the query with the required joins or includes.
3. A mapper converts the persistence result into the projection, through its `create()` factory.
4. The use case returns the projection, and the presenter converts it to the public response format.

## Rules

- Do not add data from another entity to an entity or aggregate root just to serve an enriched query; create or reuse the appropriate projection.
- Keep joins, includes and ORM types in the infrastructure. The projection receives explicit data; it does not query repositories, the database or global configuration.
- Do not treat the projection as an HTTP DTO: it is independent of the transport, and the presenter defines the public response, including the names of the exposed fields.
- Do not use a projection to perform business changes in place of the entity or the aggregate root.
- IDs present in the projection's properties are references to the queried entities, not an identity of its own. They follow the policy in [domain-entity-identity.md](domain-entity-identity.md).
- Use plain collections in the projection. Lists with change tracking belong to the aggregate root; do not bring that tracking into the read side without need.
- When building the projection from values that are already stored, use the reconstitution factory of the inner value object, not the normalization one. A persisted slug, for example, is preserved as it is, not generated again from the title.
- When reading from a cache, `JSON.parse()` alone does not reconstitute instances, getters, dates or ID wrappers. Reconstitute the projection through the mapper or the factory before returning it.

## Model

The names are illustrative. The projection gathers the comment data and the author's name; `commentId` and `authorId` are references.

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

The repository contract declares, for example, `findManyByQuestionIdWithAuthor(...): Promise<CommentWithAuthor[]>`. The infrastructure mapper calls `CommentWithAuthor.create(...)` with the query data, and the presenter decides how to expose `author` in the response.
