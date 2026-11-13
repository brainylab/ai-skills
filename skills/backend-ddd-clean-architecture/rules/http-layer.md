# Camada de apresentação — entrada e saída

- Controllers validam a requisição, chamam casos de uso e traduzem o resultado para o transporte.
- Todos os schemas de validação de um controller (`query`, `params`, `body`, `headers` e `response`) ficam no próprio arquivo do controller.
- Não crie arquivos separados para schemas dos controllers. Valide-os pelo fluxo HTTP nos testes E2E, usando os schemas registrados pelo controller, sem duplicar suas definições.
- Presenters transformam os resultados em respostas públicas, sem executar consultas ou regras de negócio.
- Factories conectam casos de uso, repositórios e adapters concretos.
- O framework HTTP pode ser usado na apresentação e na infraestrutura HTTP, nunca no domínio ou na aplicação.
