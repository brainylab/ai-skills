# Tests

- Keep `*.spec.ts` files next to the unit under test.
- Use the `*.e2e.spec.ts` suffix for E2E tests, next to the controller and with the same name, such as `create-user-controller.e2e.spec.ts`. They may be excluded from the default run and executed by their own command, depending on the project configuration.
- Use `test/factories/` to build test objects and data, and `test/repositories/` for in-memory repositories. Fakes of gateways and ports live in folders named after their kind, such as `test/gateways/` and `test/notifiers/`.
- Unit tests of the domain and application layers must not require a database, the network or an HTTP server.
- Test concrete repositories and integrations in integration tests, with controlled dependencies.
- Controllers must have E2E tests only, exercising the application's HTTP routes and covering validation, error translation and response format. Do not create isolated unit or integration tests for controllers or their schemas.
