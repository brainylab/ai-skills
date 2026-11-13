# Camada de infraestrutura — implementações técnicas

- Implementa repositórios e ports definidos pelas camadas internas.
- Contém consultas, schemas, mappers e integrações com ferramentas externas.
- Mappers convertem dados de persistência em objetos do domínio e vice-versa, evitando expor tipos do ORM às camadas internas.
- Detalhes exclusivos de um módulo ficam em sua `infrastructure/`; conexão de banco, servidor HTTP e outros recursos compartilhados ficam em `src/infra/`.
- Configuração de ambiente é resolvida na infraestrutura e os valores necessários são injetados nas dependências.
- Organize os serviços técnicos compartilhados com `adapters/` para implementações concretas, `contracts/` para interfaces técnicas e `factory/` para criação e configuração das instâncias, quando essa separação for necessária.
- Contratos de infraestrutura descrevem operações técnicas, como iniciar e encerrar conexões ou servidores. Eles não substituem os contratos de repositórios e ports da aplicação e não devem ser importados pelos casos de uso.
- Quando a ferramenta exigir agregadores globais de schemas ou relações, mantenha-os em `infra/database/`, sem torná-los contratos do domínio.
