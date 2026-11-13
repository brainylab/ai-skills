# Infraestrutura: organização e nomenclatura

Estrutura de referência para recursos compartilhados:

```text
src/
├── gateways/
│   └── secrets/
│       └── secrets-gateway.ts           # SecretsGateway: contrato da aplicação
├── infra/
│   ├── app.ts                           # composição do ciclo de vida
│   ├── env.ts                           # leitura e validação do ambiente
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
    └── <modulo>/
        ├── application/
        │   ├── ports/                   # contratos exclusivos do módulo
        │   └── repositories/            # contratos de persistência do módulo
        └── infrastructure/              # implementações exclusivas do módulo
```

- Use arquivos em `kebab-case`, contratos e classes em `PascalCase` e factories com prefixo `make`.
- Nomeie contratos técnicos como `DatabaseContract` e `HttpContract`; gateways como `SecretsGateway`, em `<nome>-gateway.ts`; adapters pela tecnologia e capacidade, como `AwsSmSecretsAdapter`, em `<tecnologia>-<nome>-adapter.ts`.
- Nomeie contratos de repositório no plural, como `UserRepositories` em `user-repositories.ts`, e suas implementações pela tecnologia, como `DrizzleUserRepositories` e `InMemoryUserRepositories`.
- Use `adapters/` e `factory/` em todos os recursos de `src/infra/`, inclusive gateways. Em projetos existentes, preserve a convenção local de singular/plural das pastas.
- Use a grafia `infrastructure/` para a infraestrutura exclusiva dos módulos. Reserve `src/infra/` para a infraestrutura compartilhada, conforme [arch-conventions.md](arch-conventions.md).
- Não crie diretórios vazios. Um gateway não exige `contracts/` nem `index.ts` quando seu próprio contrato já atende à implementação; crie `contracts/` somente no caso descrito em [infra-gateways.md](infra-gateways.md).
