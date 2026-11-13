# Infrastructure: organization and naming

Reference structure for shared resources:

```text
src/
├── gateways/
│   └── secrets/
│       └── secrets-gateway.ts           # SecretsGateway: application contract
├── infra/
│   ├── app.ts                           # lifecycle composition
│   ├── env.ts                           # environment loading and validation
│   ├── database/
│   │   ├── contracts/
│   │   │   └── database-contract.ts
│   │   ├── adapters/
│   │   │   └── <driver>-adapter.ts
│   │   ├── factory/
│   │   │   └── make-database.ts
│   │   └── index.ts
│   ├── http/
│   │   ├── contracts/
│   │   │   └── http-contract.ts
│   │   ├── adapters/
│   │   │   └── <framework>-adapter.ts
│   │   ├── factory/
│   │   │   └── make-server.ts
│   │   └── index.ts
│   └── gateways/
│       └── secrets/
│           ├── adapters/
│           │   └── <provider>-secrets-adapter.ts
│           └── factory/
│               └── make-secrets-gateway.ts
└── modules/
    └── <module>/
        ├── application/
        │   ├── ports/                   # contracts exclusive to the module
        │   └── repositories/            # persistence contracts of the module
        └── infrastructure/              # implementations exclusive to the module
```

- Use `kebab-case` files, `PascalCase` contracts and classes, and the `make` prefix for factories.
- Name technical contracts like `DatabaseContract` and `HttpContract`; gateways like `SecretsGateway`, in `<name>-gateway.ts`; adapters after the technology and the capability, like `AwsSmSecretsAdapter`, in `<technology>-<name>-adapter.ts`.
- Name repository contracts in the plural, like `UserRepositories` in `user-repositories.ts`, and their implementations after the technology, like `DrizzleUserRepositories` and `InMemoryUserRepositories`.
- Use `adapters/` and `factory/` in every resource under `src/infra/`, including gateways. In existing projects, preserve the local singular/plural convention for folders.
- Use the spelling `infrastructure/` for the infrastructure exclusive to a module. Reserve `src/infra/` for shared infrastructure, as described in [arch-conventions.md](arch-conventions.md).
- Do not create empty directories. A gateway does not require `contracts/` or `index.ts` when its own contract already serves the implementation; create `contracts/` only in the case described in [infra-gateways.md](infra-gateways.md).
