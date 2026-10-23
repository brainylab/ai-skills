# Guideline Strutura

Modular Monolith com Clean Architecture

```text
src/
├── modules/
│   ├── users/
│   │   ├── domain/
│   │   │   ├── aggregates/
│   │   │   ├── entities/
│   │   │   ├── value-objects/
│   │   │   ├── events/
│   │   │   ├── repositories/
│   │   │   └── services/
│   │   │
│   │   ├── application/
│   │   │   ├── use-cases/
│   │   │   ├── commands/
│   │   │   ├── queries/
│   │   │   └── ports/
│   │   │
│   │   ├── infrastructure/
│   │   │   ├── database/
│   │   │   │   ├── schema/
│   │   │   │   └── migrations/
│   │   │   ├── repositories/
│   │   │   ├── adapters/
│   │   │   ├── integrations/
│   │   │   └── messaging/
│   │   │
│   │   ├── presentation/
│   │   │   ├── http/
│   │   │   │   ├── controllers/
│   │   │   │   └── ws/
│   │   │   └── events/
│   │   │
│   │   └── tests/
│   │       ├── fakes/
│   │       ├── fixtures/
│   │       └── factories/
│   │
├── core/
│   ├── domain/
│   └── application/
│
├── gateways/                     # contratos de gateways compartilhados
│
├── infrastructure/               # implementações de infraestrutura compartilhada
│   ├── database/
│   ├── gateways/
│   ├── messaging/
│   └── http/
│
├── config/
└── main.ts
```

# Ports e Adapters entre Bounded Contexts

O objetivo de **Ports + Adapters** é permitir que um Bounded Context dependa de uma capacidade de outro contexto sem depender diretamente da implementação dele.

## Conceito

```text
┌──────────────┐
│   Orders     │
│              │
│ Application  │
│              │
│ ProductPort  │  ← PORT
└──────┬───────┘
       │
       ↓
┌──────────────────────┐
│ CatalogProductAdapter│  ← ADAPTER
└──────────┬───────────┘
           │
           ↓
       ┌─────────┐
       │ Catalog │
       └─────────┘
```

### Port

O **Port** define **o que o contexto precisa**.

Exemplo:

```ts
interface ProductReader {
  findById(productId: string): Promise<ProductData | null>
}
```

O contexto `Orders` não precisa saber se o `Catalog` está no mesmo processo, em outro servidor ou se a comunicação acontece via HTTP, gRPC etc.

### Adapter

O **Adapter** define **como essa necessidade é atendida**.

Hoje, se os contextos estão no mesmo processo:

```ts
class CatalogProductAdapter implements ProductReader {
  async findById(productId: string) {
    return catalogProductRepository.findById(productId)
  }
}
```

No futuro, se o `Catalog` virar um serviço separado:

```ts
class CatalogProductAdapter implements ProductReader {
  async findById(productId: string) {
    return httpClient.get(`/products/${productId}`)
  }
}
```

O `Orders` continua utilizando:

```ts
productReader.findById(productId)
```

sem precisar alterar sua regra de negócio.

## Nem toda comunicação entre contextos precisa de Port + Adapter

Não devemos criar um Port + Adapter para toda comunicação entre Bounded Contexts.

Para uma simples leitura, quando os contextos utilizam o mesmo banco:

```text
OrderItem
    │
    └── productId
          │
          ↓
        JOIN
          │
          ↓
       Product
```

Pode-se utilizar diretamente uma Query/Repository com `JOIN`.

Port + Adapter faz mais sentido quando existe uma **capacidade ou serviço que outro contexto fornece**, especialmente quando existe uma possibilidade real de mudar a implementação.

Exemplos:

```text
Orders
   │
   ├── ProductReader        → ler produto
   ├── ProductAvailability  → verificar/reservar estoque
   └── PaymentGateway       → processar pagamento
```

## Gateways

Um **Gateway** é uma implementação especializada de Adapter para comunicação com algo externo ao contexto.

Exemplos:

```text
PaymentGateway
StorageGateway
NotificationGateway
CatalogGateway
```

O contrato deve representar **o que a aplicação precisa**, enquanto a implementação fica na infraestrutura.

```text
Application
    │
    ↓
PaymentGateway          ← contrato
    │
    ↓
StripePaymentGateway    ← implementação
```

Quando o Gateway pertence exclusivamente a um Bounded Context, seu contrato deve ficar próximo da aplicação, normalmente em:

```text
application/
└── ports/
```

Quando o Gateway é realmente compartilhado entre módulos, pode utilizar:

```text
gateways/
```

As implementações ficam em:

```text
infrastructure/
└── gateways/
```

## Testes e implementações Fake

As implementações reais ficam em `infrastructure`. As implementações utilizadas exclusivamente pelos testes ficam em `tests/fakes`.

Exemplo:

```text
orders/
├── application/
│   └── ports/
│       └── product-reader.ts
│
├── infrastructure/
│   └── adapters/
│       └── catalog-product-adapter.ts
│
└── tests/
    └── fakes/
        └── fake-product-reader.ts
```

A Fake deve implementar o mesmo Port utilizado pela aplicação:

```text
             ┌─────────────────┐
             │  ProductReader  │
             │      PORT       │
             └────────┬────────┘
                      │
             ┌────────┴─────────┐
             ↓                  ↓
   CatalogProductAdapter   FakeProductReader
        (produção)              (teste)
```

Dessa forma, o código da aplicação não precisa saber se está utilizando uma implementação real ou uma Fake.

Além das Fakes, podem existir recursos compartilhados pelos testes:

```text
tests/
├── fakes/
├── fixtures/
└── factories/
```

* **`fakes/`** → implementações falsas de Ports, Repositories ou Gateways.
* **`fixtures/`** → dados estáticos utilizados nos testes.
* **`factories/`** → criação padronizada de entidades e objetos de teste.

Os testes podem utilizar a convenção:

```text
*.spec.ts
*.integration.spec.ts
*.e2e.spec.ts
```

## Regra

> **Port = o que eu preciso.**
> **Adapter = como eu consigo isso.**

Um Bounded Context não deve acessar diretamente o domínio interno de outro contexto.

## Bounded Context não significa serviço separado

Bounded Contexts podem estar separados conceitualmente sem necessariamente serem serviços independentes.

Por exemplo:

```text
┌───────────────────────────────┐
│           Aplicação           │
│                               │
│   ┌─────────┐   ┌─────────┐   │
│   │ Orders  │   │ Catalog │   │
│   └─────────┘   └─────────┘   │
│                               │
└───────────────────────────────┘
```

Eles podem estar no mesmo processo e utilizar o mesmo PostgreSQL.

Se futuramente forem separados em serviços:

```text
Orders Service  ──HTTP──>  Catalog Service
```

a implementação do **Adapter** pode ser alterada sem precisar alterar o domínio de `Orders`.

### Princípio final

A separação é principalmente sobre **responsabilidade e modelo de negócio**, e não necessariamente sobre processos, servidores ou bancos de dados.

```text
Port
  ↓
define a necessidade

Adapter / Gateway
  ↓
implementa a necessidade

Fake
  ↓
implementação utilizada nos testes

Bounded Context
  ↓
não conhece a implementação interna de outro contexto
```
