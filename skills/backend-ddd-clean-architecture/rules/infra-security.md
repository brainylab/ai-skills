# Infrastructure: errors, configuration and security

- Validate the environment centrally and inject only the values each dependency needs.
- Do not commit secrets, and do not log credentials, keys, tokens, authenticated URLs or full provider responses.
- Define timeouts and, when needed, retries that are bounded and compatible with the idempotency of the operation. Do not retry indefinitely, and do not hide unavailability behind default values.
- When secrets are cached, define expiration, invalidation, isolation and how the cache relates to rotation and revocation. A cache must not bypass a requirement for immediate revocation.
- Do not weaken TLS, identity validation or authorization to work around integration errors.
- When the runtime environment has no internet access, integrations must work without it. The presence of a cloud adapter does not prove that private connectivity is available or approved.
- Changes to cryptography or secret storage require considering compatibility, integrity, key management and migration; do not copy example algorithms as a security decision.
