# Infraestrutura: contracts técnicos

Defina somente as operações exigidas pelo recurso. Banco e servidor HTTP podem seguir estes modelos:

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

- O adapter implementa o contrato com o tipo concreto da tecnologia.
- Defina o comportamento de `connection()` e `server()` antes da inicialização e após o encerramento; não retorne uma referência inválida silenciosamente.
- A obtenção de uma referência não deve esconder conexão assíncrona. Deixe a inicialização explícita em `start()` quando esse for o ciclo de vida adotado.
- Não imponha `start()` e `stop()` a integrações sem recursos a inicializar ou liberar.
- Não duplique tipos auxiliares entre `contracts/` e `index.ts`. Declare-os em um único lugar e reexporte quando necessário.
- Preserve tipos concretos necessários aos repositórios. Uma factory de banco pode retornar `DatabaseContract<ConcreteConnection>` ou manter a inferência do adapter; não apague o tipo da conexão com uma anotação genérica que o reduza a `unknown`.
