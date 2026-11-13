# Camada de domínio — regras de negócio

- Entidades representam conceitos com identidade; aggregate roots delimitam grupos de objetos cuja consistência é controlada em conjunto.
- Value objects representam valores e suas invariantes, como um e-mail válido.
- Regras de negócio ficam no domínio, não em controllers ou repositórios de banco.
- O domínio não conhece requisições HTTP, ORM, variáveis de ambiente ou SDKs externos.
- Adicione eventos e serviços de domínio quando houver necessidade, sem criar estruturas vazias antecipadamente.
