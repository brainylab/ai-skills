# Example flow across layers

To register a user:

1. `CreateUserController` validates the input and calls `CreateUserUseCase`.
2. `CreateUserUseCase` queries the `UserRepositories` contract to check whether the email is already in use.
3. The use case uses `PasswordHasher` to generate the hash and creates the `User` entity, respecting the domain invariants.
4. The concrete implementation of `UserRepositories` converts the data and persists the user.
5. The use case returns an explicit output, without credentials; the presenter formats that output and the controller responds with the appropriate HTTP status.

The factory injects the concrete repository and hasher into the use case. In unit tests, these dependencies are replaced by in-memory implementations or fakes.
