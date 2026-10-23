# Usar a skill em outros projetos

Crie um link simbólico para reutilizar a skill sem copiar os arquivos (macOS/Linux).

## Em um projeto específico

Na raiz do projeto de destino, execute:

```sh
mkdir -p .agents/skills/backend-clean-architecture
ln -s /Users/andrefelipeschulle/Developer/brainylab/guidelines/backend-clean-architecture/SKILL.md .agents/skills/backend-clean-architecture/SKILL.md
```

Ajuste o caminho absoluto caso este repositório esteja em outro local. Apenas o `SKILL.md` será vinculado; os demais arquivos não serão incluídos.

## Em todos os projetos

Para disponibilizar a skill globalmente no Zed:

```sh
mkdir -p ~/.agents/skills/backend-clean-architecture
ln -s /Users/andrefelipeschulle/Developer/brainylab/guidelines/backend-clean-architecture/SKILL.md ~/.agents/skills/backend-clean-architecture/SKILL.md
```

Escolha uma das opções e inicie uma nova conversa no Zed para carregar a skill.

## Observações

- Alterações na skill original ficam disponíveis pelo link, sem precisar copiar novamente.
- Não mova nem exclua o `SKILL.md` original: isso quebra o link.
- Se você já vinculou a pasta inteira, remova esse link com `unlink .agents/skills/backend-clean-architecture` antes de executar os novos comandos (para a instalação global, use `unlink ~/.agents/skills/backend-clean-architecture`).
- Se o destino já existir, confira antes de removê-lo; os comandos não substituem arquivos existentes.
- Links com caminho absoluto são locais à sua máquina. Outros colaboradores precisam criar seus próprios links.

Para remover apenas o link de um projeto, execute na raiz dele:

```sh
unlink .agents/skills/backend-clean-architecture/SKILL.md
```
