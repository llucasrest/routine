---
name: estudo-do-dia
description: Gera um estudo extra para o dia a partir do que já foi feito no plano de 16 semanas. Lê o progresso (checklists, notas, extras, git), pergunta o tempo disponível, sugere temas e, ao escolher, cria o arquivo em treino/extras/ e o liga à semana atual. Use quando o usuário pedir "estudo do dia", "o que estudar hoje", "gerar estudo extra" ou "tenho X minutos para estudar".
---

# Estudo do dia

Cria um estudo extra sob medida para hoje e o registra na árvore do repositório, para manter todo o contexto.

Estrutura do repositório: `treino/` (semanas, notas, recursos, projeto, extras) e `profissional/` (semanas, candidaturas, histórias STAR, estratégia). Extras ficam em `treino/extras/`.

## 1. Levantar o contexto (sem perguntar nada ainda)

Leia, nesta ordem:

1. `README.md` e `treino/projeto.md`: meta e projeto de portfólio.
2. `treino/semanas/semana-NN.md` e `profissional/semanas/semana-NN.md` de todas as semanas: itens `- [x]` são feitos, `- [ ]` pendentes. **Semana atual = a primeira com tarefas pendentes.**
3. `treino/notas/` (notas já escritas), `treino/extras/` (estudos extras já gerados, para não repetir tema) e `treino/recursos.md`.
4. `git log --oneline -15` para ver o ritmo recente.
5. A data de hoje e o dia da semana (seg = leitura, ter = system design, qua = IA/cursos/certificação/Kotlin, qui = projeto, sex = carreira, sáb = bônus).

Resuma em 3 a 5 linhas: semana atual, o que está pendente, o último tema estudado e o que apareceu nas anotações ("O que travou").

## 2. Perguntar

Use AskUserQuestion, em uma única chamada:

- **Tempo disponível**: 30 min, 1 h, 2 h ou outro.
- **Foco**: o que o dia pede pela rotina, pendências, reforço de tema, ou livre.

## 3. Sugerir

Proponha 3 a 4 temas que caibam no tempo, cada um com título, por que agora (ligado ao progresso) e o resultado esperado. Misture:

- **Pendência**: algo da semana atual ou anterior que ficou sem marcar.
- **Reforço**: aprofunda uma nota ou tema recente (ex.: após a leitura de outbox, um exercício de idempotência).
- **Lacuna do plano**: um item de "O que falta" do README que ainda não tem cobertura (IA aplicada, system design, repertório de casos).
- **Entrevista**: um caso de system design ou história STAR curta, se o tempo for baixo.

Ajuste a profundidade ao tempo: 30 min = um conceito + 3 perguntas de fixação; 1 h = leitura guiada + exercício; 2 h = mini-laboratório ou design completo.

Não inclua tema já coberto em `treino/extras/`. Recomende uma opção como a primeira.

## 4. Gerar e registrar

Quando o usuário escolher:

1. Crie `treino/extras/AAAA-MM-DD-slug.md` (slug curto em kebab-case) com este modelo:

```markdown
# <Título>

- **Data:** AAAA-MM-DD
- **Semana:** NN
- **Tempo:** <X min/h>
- **Tipo:** pendência | reforço | lacuna | entrevista
- **Liga com:** <arquivo da semana, nota ou projeto relacionado, como link relativo>

## Objetivo

<o que sabe ou faz ao final>

## Roteiro

- [ ] <passo 1, com tempo estimado>
- [ ] <passo 2>
- [ ] <passo 3>

## Conteúdo

<explicação, exercício, perguntas de fixação ou código, conforme o tema.
Cite fontes com links. Não copie trechos longos de terceiros.>

## Frase de entrevista

> "..."

## Resultado

- O que aprendi:
- O que ficou em aberto:
```

2. Atualize `treino/extras/README.md`: adicione uma linha na tabela (`Data | Estudo | Tempo | Semana | Status`), mais recente primeiro. Se o arquivo não existir, crie-o com a tabela.
3. Abra `treino/semanas/semana-NN.md` da semana atual e acrescente (ou complete) uma seção no fim do arquivo, antes de "Checkpoint" se existir:

```markdown
## Extras

- [ ] [<Título>](../extras/AAAA-MM-DD-slug.md) (AAAA-MM-DD, X min)
```

4. Se o tema for de carreira (STAR, currículo, negociação), registre a mesma linha em `profissional/semanas/semana-NN.md`.

## 5. Fechar

Diga em poucas linhas: o arquivo criado, onde foi ligado e o primeiro passo do roteiro. **Não faça commit nem push** a menos que o usuário peça.

## Regras

- Escreva em português; o conteúdo de notas em inglês só quando o plano pedir (notas de leitura e frases de entrevista seguem em inglês).
- Nunca marque tarefas do plano original como feitas; só o usuário decide.
- Se o estudo tiver código, compile e rode antes de entregar (copie para um diretório temporário fora do repositório) e confira que a saída bate com os comentários.
- Não invente links: use apenas fontes de `treino/recursos.md` ou que você tenha verificado.
- Não use código ou dados de empregadores nos exemplos (ver `treino/projeto.md`).
- Se não houver progresso marcado (início do plano), assuma a semana 01 e diga isso.
