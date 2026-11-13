# Infrastructure layer — technical implementations

- Implements the repositories and ports defined by the inner layers.
- Contains queries, schemas, mappers and integrations with external tools.
- Mappers convert persistence data into domain objects and back, so that ORM types are not exposed to the inner layers.
- Details exclusive to a module live in its `infrastructure/`; the database connection, the HTTP server and other shared resources live in `src/infra/`.
- Environment configuration is resolved in the infrastructure, and the required values are injected into the dependencies.
- Organize shared technical services with `adapters/` for concrete implementations, `contracts/` for technical interfaces and `factory/` for creating and configuring instances, when that separation is needed.
- Infrastructure contracts describe technical operations, such as starting and stopping connections or servers. They do not replace the application's repository contracts and ports, and must not be imported by use cases.
- When the tool requires global aggregators of schemas or relations, keep them in `infra/database/`, without turning them into domain contracts.
