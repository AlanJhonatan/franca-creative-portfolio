---
name: "pretty-commit"
description: "Ativada quando o usuário pede para realizar um commit ou salvar alterações parciais. Esta habilidade isola linhas específicas de código de um arquivo no stage usando Git interativo (hunks/line staging) e cria um commit altamente padronizado."
---

# Rules

- [IMPORTANT] Esta habilidade de commit é leve e performática. Recomenda-se executá-la utilizando modelos mais rápidos (ex: Flash) para economizar tokens. Se o modelo atual for High/Pro, o agente deve sugerir ao usuário via chat que mude para o Flash caso seja uma operação simples.
- sempre que o usuário pedir um commit ele deve fornecer também um `contexto/resumo` sobre o que foi feito para facilitar o trabalho do agente
- caso o usuário não forneça `contexto/resumo` adicional sobre o que foi feito, é dever do agente perguntar para o usuário "O que tanto você fez e quer incluir nesse commit?"
- importante olhar para o `contexto/resumo adicional do usuário` PRIMEIRO e depois olhar para o `git diff` para encontrar os Hunks/Line Staging correspondentes -- e sempre que possível, isolar esses pedaços -- ao que deve ser incluido para depois realizar os `git add` de cada parte
- usar padrão `conventional commits` para mensagem
  - `<operation>: <message>`
    Operations:
  - feat -> Quando você adiciona uma nova funcionalidade ao sistema.
  - fix -> Quando você realiza a correção de um bug ou comportamento inesperado.
  - refactor -> Mudança estrutural que não corrige bug e não adiciona feature (limpeza de código).
  - style -> Alterações apenas de formatação, espaços ou ponto e vírgula (sem mexer na lógica).
  - docs -> Modificações exclusivamente em arquivos de documentação (como o README.md).
  - perf -> Alterações de código com foco estrito em ganho de performance.
  - chore -> Atualizações de tarefas de build, pacotes, infraestrutura ou arquivos de configuração.
  - ci -> Mudanças em scripts de integração e deploy contínuo (ex: GitHub Actions).
    Message:
  - Use o modo imperativo: Escreva a mensagem como uma ordem ou instrução atual
  - Letras minúsculas: A primeira letra após o espaço não deve ser maiúscula
  - Sem ponto final: Não encerre a frase com ponto ou caracteres especiais no fim da descrição.
  - Limite de caracteres: Mantenha a primeira linha com no máximo 50 a 72 caracteres para evitar quebras em ferramentas de CLI.
- Pode usar emojis, ta liberado.

# Steps

1. Se o usuário não forneceu `contexto/resumo adicional` PEÇA À ELE, É OBRIGATÓRIO
2. Execute o comando `git status` e `git diff` para traçar tudo relacionado ao que o usuario pediu
3. Se houver modificações diferentes fora do escopo que o usuário pediu, identifique os arquivos e linhas alvos e execute `git add -p <caminho_do_arquivo>` para selecionar precisamente, do contrário, pode ir pro `git add` da pasta e ir pro `step 9`.
4. Quando o prompt do Git abrir, envie `s` para dividir (split) o hunk em pedaços menores.
5. Digite `y` apenas nos blocos de linhas correspondentes à tarefa atual e `n` nos demais.
6. Digite `q` para sair do modo interativo se terminar antes do fim do arquivo.
7. Valide o resultado com `git diff --cached` antes de prosseguir para o commit.
8. Se tiver algo errado, pode refazer baseado nos steps anteriores
9. Gere a mensagem no formato conventional commits (explicito em RULES)
10. Faça o output dos commits e messages, e peça a validação do usuário.

# Template

[operation]: message

features on this commit:

- <alteração detalhada 1 baseada nas linhas do stage>
- <alteração detalhada 2 baseada nas linhas do stage>
