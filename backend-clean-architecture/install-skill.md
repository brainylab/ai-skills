# Usar a skill em outros projetos

Crie um link simbólico para a pasta inteira da skill, sem copiar os arquivos (macOS/Linux). Assim, o `SKILL.md` e os arquivos de apoio ficam disponíveis juntos, permitindo consultar referências de estrutura ou entidades por caminhos relativos quando necessário.

## Em um projeto específico

Na raiz do projeto de destino, execute:

```sh
mkdir -p .agents/skills
ln -s /Users/andrefelipeschulle/Developer/brainylab/guidelines/backend-clean-architecture .agents/skills/backend-clean-architecture
```

Ajuste o caminho absoluto caso este repositório esteja em outro local. O destino `.agents/skills/backend-clean-architecture` não deve existir antes de executar `ln -s`; se você usava a instalação anterior, siga a seção de migração abaixo.

## Em todos os projetos

Para disponibilizar a skill globalmente no Zed:

```sh
mkdir -p ~/.agents/skills
ln -s /Users/andrefelipeschulle/Developer/brainylab/guidelines/backend-clean-architecture ~/.agents/skills/backend-clean-architecture
```

O destino `~/.agents/skills/backend-clean-architecture` também não deve existir antes de executar `ln -s`.

Escolha uma das opções e inicie uma nova conversa no Zed para carregar a skill.

## Migrar a instalação anterior

Se você criou uma pasta contendo apenas o link para `SKILL.md`, confira se esse arquivo é realmente um link simbólico e se não há arquivos locais que precisam ser preservados. Depois, remova o link antigo e a pasta vazia.

Para a instalação em um projeto, execute na raiz dele:

```sh
unlink .agents/skills/backend-clean-architecture/SKILL.md
rmdir .agents/skills/backend-clean-architecture
```

Para a instalação global:

```sh
unlink ~/.agents/skills/backend-clean-architecture/SKILL.md
rmdir ~/.agents/skills/backend-clean-architecture
```

O `rmdir` só remove pastas vazias. Se falhar, confira o conteúdo restante antes de prosseguir; não apague arquivos locais indiscriminadamente. Após remover a pasta antiga, execute os comandos de instalação da opção escolhida. Se você já vinculou a pasta inteira corretamente, não precisa migrar.

## Observações

- Alterações na skill original ficam disponíveis pelo link, sem precisar copiar novamente.
- Não mova nem exclua a pasta original: isso quebra o link.
- Mantenha o `SKILL.md` e os arquivos de apoio dentro da pasta original. Referencie os arquivos de apoio por caminhos relativos no `SKILL.md` e indique quando consultá-los.
- Se o destino já existir, confira antes de executar a instalação: `ln -s` pode criar um link dentro de uma pasta existente, em vez de vincular a pasta no local esperado.
- Links com caminho absoluto são locais à sua máquina. Outros colaboradores precisam criar seus próprios links.

Para remover apenas o link de um projeto, execute na raiz dele:

```sh
unlink .agents/skills/backend-clean-architecture
```

Para remover apenas o link global:

```sh
unlink ~/.agents/skills/backend-clean-architecture
```

Esses comandos removem somente os links das pastas, sem apagar os arquivos originais.
