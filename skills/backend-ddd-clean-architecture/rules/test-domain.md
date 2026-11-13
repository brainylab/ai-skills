# Tests for entities and value objects

Do not create tests for entities or aggregate roots. Between entities and value objects, create tests only for value objects, covering the behaviors defined by their contract.

For values such as `Slug`, cover the text transformation and the preservation of the value on reconstitution. For composite objects, cover factories, getters and the equality provided by the base class, without inventing guarantees of immutability or semantic comparison. Do not write `equals()` tests for classes that do not offer that method. Preserve existing tests; add new tests when needed, without changing the ones already written.
