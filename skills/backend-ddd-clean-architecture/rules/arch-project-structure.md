# Project structure

Modular Monolith with Clean Architecture.

Organize the backend into business modules inside a single application. Each module separates business rules, use cases, technical details and entry interfaces.

The goal is to keep business rules independent of the database, frameworks and external services, so that these technologies can be tested and replaced without rewriting the use cases.

## Reference structure

TypeScript example with a `users` module:

```text
src/
├── modules/
│   └── users/
│       ├── domain/
│       │   └── entities/                 # entities and aggregate roots
│       │       ├── user.ts
│       │       └── value-objects/
│       │           └── email.ts
│       │
│       ├── application/
│       │   ├── repositories/             # persistence contracts
│       │   │   └── user-repositories.ts
│       │   ├── ports/                    # contracts for external services
│       │   │   └── password-hasher.ts
│       │   └── use-case/
│       │       ├── create-user-use-case.ts
│       │       ├── create-user-use-case.spec.ts
│       │       └── errors/
│       │           └── email-already-in-use-error.ts
│       │
│       ├── infrastructure/
│       │   ├── database/
│       │   │   └── <orm>/                # for example: drizzle or prisma
│       │   │       ├── schemas/
│       │   │       ├── repositories/
│       │   │       └── mappers/
│       │   └── adapters/                 # implementations of the module ports
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
│       └── test/                         # test support for the module
│           ├── factories/
│           │   └── make-user.ts
│           └── repositories/
│               └── in-memory-user-repositories.ts
│
├── core/                                 # shared, framework-free primitives
│   ├── entities/                         # Entity, AggregateRoot, ValueObject, IDs
│   ├── errors/
│   ├── events/
│   ├── types/
│   └── use-case/                         # common input and output types
│
├── gateways/                             # contracts for shared integrations
│
├── infra/                                # shared infrastructure
│   ├── app.ts                            # startup and shutdown of services
│   ├── env.ts                            # environment loading and validation
│   ├── config/
│   ├── database/                         # database connection and configuration
│   │   ├── adapters/                     # integration with the driver or ORM
│   │   │   └── <orm>-adapter.ts
│   │   ├── contracts/                    # technical contract for access and lifecycle
│   │   │   └── database-contract.ts
│   │   ├── factory/                      # adapter creation and configuration
│   │   │   └── make-database.ts
│   │   └── index.ts                      # entry point to the database infrastructure
│   ├── gateways/                         # implementations of the shared contracts
│   ├── messaging/                        # messaging connection and configuration
│   └── http/
│       ├── adapters/                     # integration with the HTTP framework
│       │   └── <framework>-adapter.ts
│       ├── contracts/                    # technical contract of the HTTP server
│       │   └── http-contract.ts
│       ├── factory/                      # server creation and configuration
│       │   └── make-server.ts
│       ├── errors/
│       ├── helpers/
│       ├── controllers.ts                # registration of the module controllers
│       └── index.ts                      # entry point to the HTTP infrastructure
│
├── utils/                                # shared technical utilities
└── main.ts                               # process entry point and lifecycle
```

The tree is a reference, not a list of mandatory folders. Create only the structures you need. Adapt schema, relation and migration files to the chosen persistence tool.
