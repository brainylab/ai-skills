# Testes

- Mantenha arquivos `*.spec.ts` próximos da unidade testada.
- Use o sufixo `*.e2e.spec.ts` para testes E2E, ao lado do controller e com o mesmo nome, como `create-user-controller.e2e.spec.ts`. Eles podem ficar fora da execução padrão e rodar por comando próprio, conforme a configuração do projeto.
- Use `test/factories/` para construir objetos e dados de teste e `test/repositories/` para repositórios em memória. Fakes de gateways e ports ficam em pastas nomeadas pelo tipo, como `test/gateways/` e `test/notifiers/`.
- Testes unitários de domínio e aplicação não devem exigir banco, rede ou servidor HTTP.
- Teste repositórios concretos e integrações em testes de integração, com dependências controladas.
- Controllers devem ter apenas testes E2E, exercitando as rotas HTTP da aplicação e cobrindo validação, tradução de erros e formato das respostas. Não crie testes unitários ou de integração isolados para controllers ou seus schemas.
