# Exemplo de fluxo entre camadas

Para cadastrar um usuário:

1. `CreateUserController` valida a entrada e chama `CreateUserUseCase`.
2. `CreateUserUseCase` consulta o contrato `UserRepositories` para verificar se o e-mail já existe.
3. O caso de uso utiliza `PasswordHasher` para gerar o hash e cria a entidade `User`, respeitando as invariantes do domínio.
4. A implementação concreta de `UserRepositories` converte os dados e persiste o usuário.
5. O caso de uso retorna uma saída explícita, sem credenciais; o presenter formata essa saída e o controller responde com o status HTTP adequado.

A factory injeta o repositório e o gerador de hash concretos no caso de uso. Nos testes unitários, essas dependências são substituídas por implementações em memória ou fakes.
