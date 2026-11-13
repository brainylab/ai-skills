# Domain: entity model with getters and a setter

The model uses the base class signature with one generic parameter and a mandatory main identity of type `UniqueEntityID`. `configured` and `configuredAt` demonstrate the setter and its effects on dates. In this example, the model also requires the additional property `public_id: PublicEntityID`; omit that property, its import and its getter in entities that have no public ID.

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

The factory receives `id: UniqueEntityID` separately from `props.public_id: PublicEntityID`. `...props` preserves the public property that was received; it is not forwarded in place of the `id` to the base class constructor. Adjust the imports to the real locations of the primitives in the project.
