# Testes e checklist de infraestrutura

- Teste casos de uso com fakes dos gateways e repositórios em memória, sem importar factories, ambiente, SDKs, rede ou banco real.
- Teste adapters com clientes controlados para cobrir tradução de dados, respostas inválidas e falhas do provedor; use integração controlada para validar o comportamento real do driver ou SDK quando necessário.
- Teste factories relevantes quanto à seleção e configuração das dependências, sem exigir rede apenas para construir uma instância.
- Cubra ciclo de vida, inicialização parcial, limpeza após falhas e encerramento dos recursos.
- Valide controllers e seus schemas somente pelos testes E2E HTTP, conforme [test-organization.md](test-organization.md), sem criar testes isolados de controller.
- Confira se nenhum contrato de aplicação importa tipos técnicos e se nenhum caso de uso instancia implementações concretas.
- Confira se a factory preserva os tipos necessários à infraestrutura sem expô-los às camadas internas.
- Execute as validações existentes pertinentes à mudança e remova apenas os artefatos temporários gerados por sua própria validação.
