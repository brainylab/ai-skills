# Domínio: modelo de entidade com getters e setter

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
