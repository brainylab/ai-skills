# Regra de dependência

As dependências apontam para as camadas internas:

```text
application    → domain → core
infrastructure → application, domain, core e src/infra
presentation   → application, domain, core e src/infra/http
```

- `domain/` não depende de `application/`, `infrastructure/`, `presentation/` ou `infra/`.
- `application/` não depende de infraestrutura ou apresentação. Pode importar contratos compartilhados de `src/gateways/`.
- `infrastructure/` do módulo pode importar a infraestrutura compartilhada de `src/infra/`, como o ponto de acesso do banco e helpers técnicos, e os contratos de `src/gateways/`.
- `presentation/` importa de `src/infra/http/` apenas o suporte ao transporte, como o helper de criação de controllers e os erros HTTP. Não importa banco, gateways concretos nem a `infrastructure/` do módulo; essas dependências entram pelas factories.
- `core/` não depende de módulos de negócio nem de infraestrutura.
- Factories são pontos de composição: podem importar implementações concretas para injetá-las nos casos de uso.
- A regra também vale para imports de tipos: um tipo inferido do ORM continua sendo uma dependência de infraestrutura.
