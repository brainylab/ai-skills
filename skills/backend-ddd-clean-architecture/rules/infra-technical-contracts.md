# Infrastructure: technical contracts

Define only the operations the resource requires. The database and the HTTP server can follow these models:

```ts
export interface DatabaseContract<TConnection = unknown> {
  start(): Promise<void>;
  stop(): Promise<void>;
  connection(): TConnection;
}

export interface HttpContract<TServer = unknown> {
  start(): Promise<void>;
  stop(): Promise<void>;
  server(): TServer;
}
```

- The adapter implements the contract with the concrete type of the technology.
- Define the behavior of `connection()` and `server()` before startup and after shutdown; do not silently return an invalid reference.
- Obtaining a reference must not hide an asynchronous connection. Keep initialization explicit in `start()` when that is the adopted lifecycle.
- Do not impose `start()` and `stop()` on integrations that have no resources to initialize or release.
- Do not duplicate helper types between `contracts/` and `index.ts`. Declare them in a single place and re-export when needed.
- Preserve the concrete types the repositories need. A database factory may return `DatabaseContract<ConcreteConnection>` or keep the adapter's inferred type; do not erase the connection type with a generic annotation that reduces it to `unknown`.
