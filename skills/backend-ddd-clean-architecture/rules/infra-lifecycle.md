# Infraestrutura: inicialização e encerramento

- O bootstrap inicializa dependências antes dos seus consumidores: por exemplo, banco antes de aceitar requisições HTTP.
- Aguarde cada inicialização e propague falhas; não anuncie disponibilidade antes de todos os recursos obrigatórios estarem prontos.
- Se uma etapa falhar, libere os recursos já iniciados. Um recurso composto também deve limpar sua própria inicialização parcial.
- Encerre em ordem inversa, deixando de aceitar novas requisições antes de fechar dependências utilizadas por elas.
- Uma falha ao encerrar um recurso não deve impedir a tentativa de liberar os demais. Preserve a informação das falhas sem registrar dados sensíveis.
- Defina o comportamento de chamadas repetidas de `start()` e `stop()` e teste-o. Não presuma idempotência de um driver ou framework.
- Mantenha sinais e código de saída em `main.ts`; detalhes de conexão e servidor pertencem aos adapters.

Essas são diretrizes de implementação, não uma afirmação de que todos esses controles já existem no serviço.
