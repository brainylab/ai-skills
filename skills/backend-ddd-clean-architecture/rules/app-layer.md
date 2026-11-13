# Camada de aplicação — casos de uso e contratos

- Cada caso de uso representa uma ação do sistema e expõe um método `execute()` com entrada e saída explícitas.
- Recebe dependências pelo construtor, usando contratos em vez de implementações concretas.
- Contratos de persistência ficam em `application/repositories/`; contratos de outros serviços necessários ao módulo ficam em `application/ports/`.
- Coordena entidades e dependências, sem conhecer detalhes do transporte ou do banco.
- Retorna resultados e erros da aplicação; códigos HTTP são definidos na apresentação.
- Adote uma estratégia consistente para resultados e erros, como `Result`/`Either` ou exceções tipadas.
