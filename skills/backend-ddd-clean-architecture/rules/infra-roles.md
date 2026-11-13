# Infrastructure: roles of gateways, contracts, adapters and factories

Implement technical resources with a separation between **contracts**, **adapters** and **factories**, keeping use cases independent of drivers, frameworks, configuration and external SDKs.

Use the `infra-*` rules together with [infra-layer.md](infra-layer.md) and [arch-dependency-rule.md](arch-dependency-rule.md) when implementing the database, HTTP, messaging or integrations. The TypeScript examples are structural models: adapt names, aliases, signatures and the error strategy to the project, without creating abstractions or resources that are not needed.

## Responsibilities

| Element | Responsibility | Must not |
| --- | --- | --- |
| Gateway | Define the capability of an integration that the application needs. | Expose SDKs, the environment, concrete clients or the technical lifecycle to the use case. |
| Infra contract | Define the technical interface of a resource, such as connection, server and lifecycle. | Replace application contracts or carry business rules. |
| Adapter | Implement a contract using a concrete technology. | Coordinate use cases, authorize business operations or respond to HTTP outside the HTTP adapter. |
| Factory | Choose implementations and assemble their dependencies and configuration. | Run business rules or start resources implicitly. |
| `index.ts` | Expose the entry point of the resource and the technical types that are needed. | Duplicate contracts, hide asynchronous initialization or make infrastructure reachable from the inner layers. |
| Bootstrap | Coordinate startup, shutdown and failure handling of the resources. | Contain rules of the business modules. |

A gateway is an outbound contract of the application, not an HTTP server and not necessarily a concrete class. An adapter implements that contract and translates its operations to the chosen provider.
