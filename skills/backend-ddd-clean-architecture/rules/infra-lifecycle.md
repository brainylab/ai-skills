# Infrastructure: startup and shutdown

- The bootstrap starts dependencies before their consumers: for example, the database before accepting HTTP requests.
- Await each startup and propagate failures; do not announce availability before all required resources are ready.
- If a step fails, release the resources that were already started. A composite resource must also clean up its own partial initialization.
- Shut down in reverse order, stopping the intake of new requests before closing the dependencies they use.
- A failure while shutting down one resource must not prevent the attempt to release the others. Preserve the failure information without logging sensitive data.
- Define the behavior of repeated calls to `start()` and `stop()`, and test it. Do not assume a driver or framework is idempotent.
- Keep signals and exit codes in `main.ts`; connection and server details belong to the adapters.

These are implementation guidelines, not a statement that all of these controls already exist in the service.
