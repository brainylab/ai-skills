# Infraestrutura: erros, configuração e segurança

- Valide ambiente centralmente e injete somente os valores necessários a cada dependência.
- Não versione segredos nem registre credenciais, chaves, tokens, URLs autenticadas ou respostas completas do provedor.
- Defina timeouts e, quando necessários, retries limitados e compatíveis com a idempotência da operação. Não repita operações indefinidamente nem esconda indisponibilidade com valores padrão.
- Quando houver cache de segredos, defina validade, invalidação, isolamento e relação com rotação e revogação. Cache não pode ignorar uma exigência de revogação imediata.
- Não enfraqueça TLS, validação de identidade ou autorização para contornar erros de integração.
- Quando o ambiente de execução não tiver acesso à internet, as integrações devem funcionar sem ela. A presença de um adapter de nuvem não comprova conectividade privada disponível ou aprovada.
- Mudanças em criptografia ou armazenamento de segredos exigem considerar compatibilidade, integridade, gerenciamento de chaves e migração; não copie algoritmos de exemplo como decisão de segurança.
