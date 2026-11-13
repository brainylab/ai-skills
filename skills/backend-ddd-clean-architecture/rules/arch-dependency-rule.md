# Dependency rule

Dependencies point toward the inner layers:

```text
application    → domain → core
infrastructure → application, domain, core and src/infra
presentation   → application, domain, core and src/infra/http
```

- `domain/` does not depend on `application/`, `infrastructure/`, `presentation/` or `infra/`.
- `application/` does not depend on infrastructure or presentation. It may import shared contracts from `src/gateways/`.
- A module's `infrastructure/` may import the shared infrastructure in `src/infra/`, such as the database entry point and technical helpers, and the contracts in `src/gateways/`.
- `presentation/` imports from `src/infra/http/` only transport support, such as the controller creation helper and HTTP errors. It does not import the database, concrete gateways or the module's `infrastructure/`; those dependencies come in through factories.
- `core/` does not depend on business modules or on infrastructure.
- Factories are composition points: they may import concrete implementations in order to inject them into use cases.
- The rule also applies to type imports: a type inferred from the ORM is still an infrastructure dependency.
