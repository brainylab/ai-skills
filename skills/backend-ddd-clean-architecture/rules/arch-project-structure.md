# Estrutura do projeto

Modular Monolith com Clean Architecture.

Organize o backend em módulos de negócio dentro de uma única aplicação. Cada módulo separa regras de negócio, casos de uso, detalhes técnicos e interfaces de entrada.

O objetivo é manter as regras de negócio independentes de banco de dados, frameworks e serviços externos, permitindo testar e substituir essas tecnologias sem reescrever os casos de uso.

## Estrutura de referência

Exemplo em TypeScript com um módulo `users`:

```text
src/
├── modules/
│   └── users/
│       ├── domain/
│       │   └── entities/                 # entidades e aggregate roots
│       │       ├── user.ts
│       │       └── value-objects/
│       │           └── email.ts
│       │
│       ├── application/
│       │   ├── repositories/             # contratos de persistência
│       │   │   └── user-repositories.ts
│       │   ├── ports/                    # contratos de serviços externos
│       │   │   └── password-hasher.ts
│       │   └── use-case/
│       │       ├── create-user-use-case.ts
│       │       ├── create-user-use-case.spec.ts
│       │       └── errors/
│       │           └── email-already-in-use-error.ts
│       │
│       ├── infrastructure/
│       │   ├── database/
│       │   │   └── <orm>/                # por exemplo: drizzle ou prisma
│       │   │       ├── schemas/
│       │   │       ├── repositories/
│       │   │       └── mappers/
│       │   └── adapters/                 # implementações de ports do módulo
│       │
│       ├── presentation/
│       │   └── http/
│       │       └── controllers/
│       │           ├── create-user-controller.ts
│       │           ├── create-user-controller.e2e.spec.ts
│       │           ├── factories/
│       │           │   └── make-create-user-use-case.ts
│       │           └── presenters/
│       │               └── user-presenter.ts
│       │
│       └── test/                         # suporte aos testes do módulo
│           ├── factories/
│           │   └── make-user.ts
│           └── repositories/
│               └── in-memory-user-repositories.ts
│
├── core/                                 # primitivas compartilhadas sem framework
│   ├── entities/                         # Entity, AggregateRoot, ValueObject, IDs
│   ├── errors/
│   ├── events/
│   ├── types/
│   └── use-case/                         # tipos comuns de entrada e saída
│
├── gateways/                             # contratos de integrações compartilhadas
│
├── infra/                                # infraestrutura compartilhada
│   ├── app.ts                            # inicialização e encerramento dos serviços
│   ├── env.ts                            # leitura e validação do ambiente
│   ├── config/
│   ├── database/                         # conexão e configuração do banco
│   │   ├── adapters/                     # integração com o driver ou ORM
│   │   │   └── <orm>-adapter.ts
│   │   ├── contracts/                    # contrato técnico de acesso e ciclo de vida
│   │   │   └── database-contract.ts
│   │   ├── factory/                      # criação e configuração do adapter
│   │   │   └── make-database.ts
│   │   └── index.ts                      # ponto de acesso à infraestrutura de banco
│   ├── gateways/                         # implementações dos contratos compartilhados
│   ├── messaging/                        # conexão e configuração de mensageria
│   └── http/
│       ├── adapters/                     # integração com o framework HTTP
│       │   └── <framework>-adapter.ts
│       ├── contracts/                    # contrato técnico do servidor HTTP
│       │   └── http-contract.ts
│       ├── factory/                      # criação e configuração do servidor
│       │   └── make-server.ts
│       ├── errors/
│       ├── helpers/
│       ├── controllers.ts                # registro dos controllers dos módulos
│       └── index.ts                      # ponto de acesso à infraestrutura HTTP
│
├── utils/                                # utilitários técnicos compartilhados
└── main.ts                               # entrada e ciclo de vida do processo
```

A árvore é uma referência, não uma lista de pastas obrigatórias. Crie apenas as estruturas necessárias. Adapte arquivos de schemas, relações e migrations à ferramenta de persistência escolhida.
