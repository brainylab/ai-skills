# Domain: value object equality

Use the `equals()` contract available in `ValueObject`. Do not add an alternative implementation just to follow this rule.

In the reference base class, `equals()` returns `false` for an argument that is null, undefined or has no `props`, and compares `JSON.stringify(vo.props)` with `JSON.stringify(this.props)`. Distinct instances can therefore be equal when their properties produce the same serialization. IDs present in the properties take part in this comparison as data; they are not a separate identity of the base class.

This strategy does not guarantee semantic equality for every type:

- The order of keys may affect the result, and properties holding `undefined` may be omitted.
- Dates are compared by their serialized representation, not by the identity of the `Date` instances.
- The comparison does not check the concrete class and does not recursively call the `equals()` methods of IDs, entities or other value objects.
- Circular references are not supported by `JSON.stringify()`.

Check the contract before relying on this comparison in a new concept. Do not extend these guarantees to independent classes: a `Slug` implemented without inheriting from `ValueObject`, for example, does not provide `equals()`.
