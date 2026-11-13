# Domínio: getters e setters

## Getters

Exponha as propriedades por getters que retornam `this.props.<campo>`. Utilize os getters herdados para identidade e datas mantidas pela base.

## Setters

Adicione setters somente aos campos que precisam permitir alteração. A presença de um getter não exige um setter correspondente.

Quando a alteração também registrar uma data específica, mantenha a sequência:

1. Atribua o valor recebido à propriedade.
2. Atualize a data correspondente com `new Date()`.
3. Chame `this.touch()` para atualizar `updatedAt`.

Se não houver uma data específica associada à alteração, não crie um campo apenas para reproduzir essa sequência.

No formato de setter mostrado em [domain-entity-example.md](domain-entity-example.md), a data é atualizada em toda atribuição, inclusive quando o valor é `false` ou igual ao anterior. Ela registra a atribuição, não necessariamente uma transição de estado. Uma política diferente precisa ser definida pelo requisito de negócio, não presumida por esta regra.

Não substitua setters por métodos adicionais nem acrescente funções privadas de normalização sem uma necessidade explícita.
