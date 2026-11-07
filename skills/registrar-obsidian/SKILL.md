---
name: registrar-obsidian
description: Registra qualquer coisa no cofre (vault) do Obsidian do usuario, seja documento, conversa, link, ideia, decisao, aprendizado, projeto ou trecho de codigo, criando uma nota organizada e alimentando o MAPA (indice/sumario central em "00 - MAPA.md") para facilitar a navegacao e a busca. Use SEMPRE que o usuario digitar "/registrar-obsidian", ou pedir "registra no obsidian", "salva isso no obsidian", "guarda essa conversa", "anota no meu cofre", "adiciona no mapa". Use tambem quando o usuario perguntar "onde esta X no obsidian", "ja registrei algo sobre Y?", "procura no meu cofre": nesse caso leia o MAPA primeiro, antes de varrer as pastas.
---

# Registrar no Obsidian + MAPA

Esta skill faz duas coisas sempre juntas:

1. **Registra** o que o usuario pediu como uma nota `.md` no cofre do Obsidian.
2. **Alimenta o MAPA** (`00 - MAPA.md` na raiz do cofre), o indice central que diz o que existe, onde fica e do que se trata.

O MAPA e a porta de entrada do cofre, tanto para o usuario quanto para voce. Uma nota que nao entra no MAPA conta como nao registrada.

## Regras fixas

1. **Nunca registre sem atualizar o MAPA**, e nunca atualize o MAPA sem a nota existir. As duas coisas acontecem no mesmo registro.
2. **Nunca apague nem reescreva notas existentes do usuario.** Para complementar algo, acrescente uma secao `## Atualizacao AAAA-MM-DD` no fim da nota.
3. **Edite o MAPA apenas entre os marcadores `<!-- ... -->`.** O usuario pode escrever o que quiser fora deles, e esse texto deve ser preservado.
4. **Datas no formato `AAAA-MM-DD`**, com a data de hoje.
5. **Nomes de arquivo sem caracteres proibidos** (`\ / : * ? " < > |` e `#^[]`). Troque por `-`.
6. **Nada de segredos.** Se o conteudo tiver senha, token, chave de API, CPF ou cartao, mascare (`sk-****`, `***.***.***-12`) e avise o usuario.
7. **Texto em portugues do Brasil**, direto e curto.

## Passo 1: localizar o cofre

Siga esta ordem e pare na primeira que funcionar:

1. Leia `~/.claude/registrar-obsidian.json`. Se existir e tiver `{"cofre": "<caminho>"}` com uma pasta `.obsidian/` dentro, use esse caminho.
2. Se a pasta de trabalho atual (ou uma pasta acima dela) tiver `.obsidian/`, use essa pasta.
3. Procure cofres nos lugares comuns, sem descer mais que 4 niveis:
   `~/Documents`, `~/Documentos`, `~/OneDrive`, `~/iCloudDrive`, `~/Library/Mobile Documents/iCloud~md~obsidian/Documents`, `~/Obsidian`, `~/Desktop`.
   Por exemplo: `find <pasta> -maxdepth 4 -type d -name .obsidian`.
   - Achou um: confirme com o usuario numa frase ("Vou usar o cofre X, certo?").
   - Achou varios: pergunte qual usar, com opcoes.
4. Nao achou nenhum: pergunte o caminho do cofre.

Depois de confirmar, salve `{"cofre": "<caminho absoluto>"}` em `~/.claude/registrar-obsidian.json` para nao perguntar de novo.

Se o ambiente nao tem acesso ao computador do usuario (sessao na nuvem sem a pasta do cofre), diga isso claramente e ofereca gerar a nota e o trecho do MAPA como arquivos para o usuario colar. Nunca finja que registrou.

## Passo 2: garantir que o MAPA existe

- Se `<cofre>/00 - MAPA.md` nao existe, crie a partir de [MAPA-TEMPLATE.md](MAPA-TEMPLATE.md), trocando `{{DATA}}` pela data de hoje.
- Se o cofre ja tinha notas antes do MAPA, pergunte uma vez: "Quer que eu indexe as notas que ja existem no MAPA?". Se a resposta for sim, faca o **Inventario inicial** (veja abaixo).
- Se o MAPA existe mas esta sem algum marcador, recoloque o marcador no lugar certo sem apagar o que ja esta escrito.

## Passo 3: entender o que registrar

O "isso" do pedido pode ser:

| O usuario mandou | O que registrar |
|---|---|
| Arquivo anexado ou caminho (PDF, DOCX, imagem, planilha, .md) | Copie o arquivo para `<cofre>/Registros/Anexos/` e crie uma nota que resume o conteudo e aponta para o anexo com `![[arquivo]]` ou `[[arquivo]]` |
| "esta conversa", "o que fizemos", sem argumento | Resumo da conversa atual: objetivo, o que foi decidido, o que foi feito, comandos e trechos importantes, pendencias |
| Link (URL) | Titulo, do que se trata, por que importa, o link |
| Texto colado ou ditado | O proprio texto, organizado, com um resumo em cima |
| Ideia, decisao, tarefa | Nota curta com contexto e proximo passo |

Se nao der para saber o que registrar, pergunte em uma frase. Na duvida entre o pedido e a conversa inteira, registre o pedido.

## Passo 4: classificar

Escolha **uma** categoria. Cada categoria tem uma pasta e uma secao no MAPA:

| Categoria | Pasta | Quando usar |
|---|---|---|
| Conversas | `Registros/Conversas` | Resumos de conversas com IA ou reunioes |
| Documentos | `Registros/Documentos` | Arquivos, PDFs, contratos, planilhas, relatorios |
| Projetos | `Registros/Projetos` | Um projeto, app, cliente ou sistema e o andamento dele |
| Aprendizados | `Registros/Aprendizados` | Como fazer algo, tutoriais, solucoes de erro, comandos |
| Ideias | `Registros/Ideias` | Ideias, rascunhos, planos futuros |
| Referencias | `Registros/Referencias` | Links, ferramentas, skills, plugins, cursos, livros |
| Decisoes | `Registros/Decisoes` | Decisoes tomadas e o motivo de cada uma |
| Pessoas | `Registros/Pessoas` | Contatos, clientes, parceiros |

- Quando o registro pertence a um projeto ja citado no MAPA, linke esse projeto na nota (`Projeto: [[...]]`).
- So crie categoria nova quando nenhuma serve **e** o usuario concordar. Para criar, acrescente pasta, linha nesta tabela do MAPA (secao "Categorias") e nova secao com marcadores no mesmo padrao.
- Escolha de 2 a 5 tags curtas, minusculas, sem acento e com hifen (`#python`, `#cliente-acme`, `#deploy`). **Reaproveite as tags que ja aparecem no "Indice de tags" do MAPA** antes de criar novas.

## Passo 5: evitar duplicata

Antes de criar a nota, procure no MAPA (e com `grep -ril` em `Registros/`) por titulo, link ou nome de arquivo parecido.

- Se for a mesma coisa: **complemente a nota existente** (secao `## Atualizacao AAAA-MM-DD`) e atualize a linha dela no MAPA. Nao crie outra.
- Se for parecido mas diferente: crie a nova e linke as duas uma na outra em "Relacionados".

## Passo 6: criar a nota

Caminho: `<cofre>/Registros/<Categoria>/AAAA-MM-DD - <Titulo curto>.md`

Use o modelo de [NOTA-TEMPLATE.md](NOTA-TEMPLATE.md). O essencial:

- Frontmatter com `tipo`, `data`, `tags`, `fonte`, `resumo`.
- O `resumo` tem **uma frase** de ate cerca de 140 caracteres que responde "o que e isso e por que guardei". E essa frase que vai para o MAPA, entao escreva pensando em quem vai procurar depois.
- Primeira linha do corpo: `Mapa: [[00 - MAPA]]`, para voltar ao indice.
- Secao "Relacionados" com `[[links]]` para notas do cofre que tenham a ver (procure no MAPA).

## Passo 7: alimentar o MAPA

Abra `00 - MAPA.md` e, **so dentro dos marcadores**:

1. **Categoria**: dentro de `<!-- CAT:<Categoria>:INICIO -->` e `<!-- CAT:<Categoria>:FIM -->`, adicione no topo (mais recente primeiro):
   ```
   - AAAA-MM-DD · [[AAAA-MM-DD - Titulo|Titulo]]: resumo de uma frase · #tag1 #tag2
   ```
   Se existir a linha `- _vazio_`, remova.
2. **Ultimos registros**: dentro de `<!-- ULTIMOS:INICIO -->` e `<!-- ULTIMOS:FIM -->`, adicione a mesma linha no topo, com a categoria no inicio (`- AAAA-MM-DD · **Categoria** · [[...|Titulo]]`). Mantenha no maximo **15** linhas e apague as mais antigas.
3. **Indice de tags**: dentro de `<!-- TAGS:INICIO -->` e `<!-- TAGS:FIM -->`, para cada tag da nota, acrescente o link na linha da tag (`- #tag: [[nota A|A]], [[nota B|B]]`) ou crie a linha. Mantenha as linhas em ordem alfabetica.
4. **Resumo**: dentro de `<!-- RESUMO:INICIO -->` e `<!-- RESUMO:FIM -->`, atualize "Atualizado em", o total de registros e a contagem por categoria.

Se uma secao de categoria passar de 60 linhas, mova as linhas mais antigas para `Registros/<Categoria>/_Arquivo <Categoria>.md` (mesmo formato) e deixe no fim da secao: `- Mais antigos: [[_Arquivo <Categoria>]]`.

## Passo 8: confirmar ao usuario

Responda em no maximo 4 linhas:

```
Registrado: Registros/<Categoria>/AAAA-MM-DD - Titulo.md
Categoria: <Categoria> · Tags: #a #b
MAPA atualizado (<N> registros no total).
```

Acrescente uma linha extra so se algo saiu do normal (duplicata complementada, segredo mascarado, categoria nova).

## Buscar no cofre

Quando o usuario perguntar onde esta algo, ou se ja registrou algo:

1. Leia `00 - MAPA.md` primeiro e procure nos resumos, titulos e no Indice de tags.
2. So se o MAPA nao resolver, faca `grep -ril` em `Registros/` e no resto do cofre.
3. Responda com o link da nota (`Registros/...`) e o resumo dela.
4. Se achou uma nota que nao esta no MAPA, ofereca incluir.

## Inventario inicial (so quando o usuario pedir)

Para indexar um cofre que ja tinha notas:

1. Liste os `.md` do cofre, ignorando `.obsidian/`, `.trash/`, `00 - MAPA.md` e os templates.
2. Para cada nota, leia o frontmatter e o comeco do texto, e defina categoria, uma frase de resumo e tags.
3. **Nao mova as notas de lugar.** Apenas indexe no MAPA com o caminho atual (`[[caminho/nota|Titulo]]`).
4. Com mais de 50 notas, mostre ao usuario uma amostra de 10 classificacoes antes de continuar.
5. No fim, informe quantas notas foram indexadas por categoria.
