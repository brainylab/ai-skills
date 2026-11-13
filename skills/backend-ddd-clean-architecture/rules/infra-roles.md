# Infraestrutura: papéis de gateways, contracts, adapters e factories

Implemente recursos técnicos com separação entre **contracts**, **adapters** e **factories**, mantendo os casos de uso independentes de drivers, frameworks, configuração e SDKs externos.

Use as regras `infra-*` junto de [infra-layer.md](infra-layer.md) e [arch-dependency-rule.md](arch-dependency-rule.md) ao implementar banco de dados, HTTP, mensageria ou integrações. Os exemplos em TypeScript são modelos estruturais: adapte nomes, aliases, assinaturas e estratégia de erros ao projeto, sem criar abstrações ou recursos sem necessidade.

## Responsabilidades

| Elemento | Responsabilidade | Não deve fazer |
| --- | --- | --- |
| Gateway | Definir a capacidade de uma integração necessária à aplicação. | Expor SDKs, ambiente, clientes concretos ou ciclo de vida técnico ao caso de uso. |
| Contract da infra | Definir a interface técnica de um recurso, como conexão, servidor e ciclo de vida. | Substituir contratos de aplicação ou carregar regras de negócio. |
| Adapter | Implementar um contrato utilizando uma tecnologia concreta. | Coordenar casos de uso, autorizar operações de negócio ou responder HTTP fora do adapter HTTP. |
| Factory | Escolher implementações e montar suas dependências e configurações. | Executar regras de negócio ou iniciar recursos implicitamente. |
| `index.ts` | Expor o ponto de acesso do recurso e os tipos técnicos necessários. | Duplicar contratos, esconder inicialização assíncrona ou tornar infraestrutura acessível às camadas internas. |
| Bootstrap | Coordenar inicialização, encerramento e tratamento de falhas dos recursos. | Conter regras dos módulos de negócio. |

Um gateway é um contrato de saída da aplicação, não um servidor HTTP nem necessariamente uma classe concreta. Um adapter implementa esse contrato e traduz as operações para o provedor escolhido.
