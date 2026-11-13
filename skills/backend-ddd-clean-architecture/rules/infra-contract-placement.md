# Infraestrutura: localização dos contratos e direção dos imports

Distinga três tipos de contrato:

1. **Persistência do módulo:** fica em `modules/<modulo>/application/repositories/` e descreve operações sobre conceitos do domínio, não consultas SQL ou tabelas.
2. **Integração da aplicação:** fica em `modules/<modulo>/application/ports/` quando exclusiva do módulo, ou em `src/gateways/` quando realmente compartilhada. Descreve a capacidade necessária ao consumidor.
3. **Recurso técnico:** fica em `src/infra/<recurso>/contracts/` ou na infraestrutura do módulo. Descreve operações técnicas usadas por adapters, composição e outras implementações de infraestrutura.

Imports permitidos neste padrão:

```text
application/use-case → application/ports ou gateways
infra/gateways/adapter → gateway ou contrato técnico que o estende
infra/factory → adapters, contracts e configuração
infra/app → pontos de acesso dos recursos técnicos
infrastructure/repositories → contrato de persistência e infraestrutura de banco
infrastructure/adapters → port ou gateway e helpers técnicos de infra
presentation/controllers → infra/http (helper de controller e erros HTTP)
```

- Domínio e aplicação nunca importam `src/infra/`, mesmo com `import type`.
- Gateways da aplicação não importam contratos de infraestrutura. Se houver extensão, a direção é `SecretsContract extends SecretsGateway`, nunca o contrário.
- Não exponha tipos do SDK, respostas HTTP do provedor, clientes de ORM ou objetos de configuração nos gateways e contratos de repositório.
- Um contrato técnico de banco pode expor a conexão concreta por um parâmetro genérico, pois seus consumidores também são infraestrutura. Isso não autoriza o vazamento desse tipo para aplicação ou domínio.
- Use `import type` para dependências utilizadas apenas como tipos.
