# Domain: getters and setters

## Getters

Expose properties through getters that return `this.props.<field>`. Use the inherited getters for the identity and the dates kept by the base class.

## Setters

Add setters only to the fields that must allow changes. Having a getter does not require a matching setter.

When the change also records a specific date, keep this sequence:

1. Assign the received value to the property.
2. Update the corresponding date with `new Date()`.
3. Call `this.touch()` to update `updatedAt`.

If there is no specific date associated with the change, do not create a field just to reproduce this sequence.

In the setter format shown in [domain-entity-example.md](domain-entity-example.md), the date is updated on every assignment, including when the value is `false` or equal to the previous one. It records the assignment, not necessarily a state transition. A different policy must be defined by the business requirement, not assumed by this rule.

Do not replace setters with additional methods, and do not add private normalization functions without an explicit need.
