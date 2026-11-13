# Presentation layer — input and output

- Controllers validate the request, call use cases and translate the result to the transport.
- All validation schemas of a controller (`query`, `params`, `body`, `headers` and `response`) live in the controller's own file.
- Do not create separate files for controller schemas. Validate them through the HTTP flow in E2E tests, using the schemas registered by the controller, without duplicating their definitions.
- Presenters transform results into public responses, without running queries or business rules.
- Factories wire use cases to concrete repositories and adapters.
- The HTTP framework may be used in the presentation layer and in the HTTP infrastructure, never in the domain or the application layer.
