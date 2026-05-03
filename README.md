# Diretrizes para Claude Code Inspiradas em Karpathy

> Conheça meu novo projeto [Multica](https://github.com/multica-ai/multica) — uma plataforma de código aberto para executar e gerenciar agentes de codificação com habilidades reutilizáveis.
>
> Siga-me no X: [https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

Um único arquivo `CLAUDE.md` para melhorar o comportamento do Claude Code, derivado das [observações de Andrej Karpathy](https://x.com/karpathy/status/2015883857489522876) sobre armadilhas de codificação com LLMs.

English | [简体中文](./README.zh.md)

## Os Problemas

Do post de Andrej:

> "Os modelos fazem suposições erradas em seu nome e simplesmente prosseguem sem verificar. Eles não gerenciam sua confusão, não buscam esclarecimentos, não expõem inconsistências, não apresentam tradeoffs, não resistem quando deveriam."

> "Eles realmente gostam de supercomplicar código e APIs, inflar abstrações, não limpam código morto... implementam uma construção inflada com mais de 1000 linhas quando 100 bastariam."

> "Eles ainda às vezes alteram/removem comentários e código que não entendem suficientemente como efeitos colaterais, mesmo que sejam ortogonais à tarefa."

## A Solução

Quatro princípios em um arquivo que abordam diretamente esses problemas:

| Princípio | Aborda |
|-----------|--------|
| **Pense Antes de Codificar** | Suposições erradas, confusão oculta, tradeoffs ausentes |
| **Simplicidade em Primeiro Lugar** | Supercomplicação, abstrações infladas |
| **Alterações Cirúrgicas** | Edições ortogonais, tocar em código indevidamente |
| **Execução Orientada a Objetivos** | Alavancagem via abordagem testes-primeiro, critérios de sucesso verificáveis |

## Os Quatro Princípios em Detalhe

### 1. Pense Antes de Codificar

**Não presuma. Não esconda confusão. Expôr os tradeoffs.**

LLMs frequentemente escolhem uma interpretação em silêncio e seguem com ela. Este princípio força um raciocínio explícito:

- **Declare premissas explicitamente** — Se houver incerteza, pergunte em vez de adivinhar
- **Apresente múltiplas interpretações** — Não escolha em silêncio quando houver ambiguidade
- **Resista quando justificado** — Se existir uma abordagem mais simples, diga
- **Pare quando confuso** — Nomeie o que está confuso e peça esclarecimento

### 2. Simplicidade em Primeiro Lugar

**Código mínimo que resolva o problema. Nada especulativo.**

Combata a tendência ao superengenho:

- Nenhum recurso além do que foi pedido
- Nenhuma abstração para código de uso único
- Nenhuma "flexibilidade" ou "configurabilidade" que não foi solicitada
- Nenhum tratamento de erro para cenários impossíveis
- Se 200 linhas pudessem ser 50, reescreva

**O teste:** Um engenheiro sênior diria que isso está supercomplicado? Se sim, simplifique.

### 3. Alterações Cirúrgicas

**Toque apenas o que for necessário. Limpe apenas a sua própria bagunça.**

Ao editar código existente:

- Não "melhore" código adjacente, comentários ou formatação
- Não refatore o que não está quebrado
- Mantenha o estilo existente, mesmo que faria diferente
- Se notar código morto não relacionado, mencione — não exclua

Quando suas alterações criarem órfãos:

- Remova imports/variáveis/funções que SUAS alterações tornaram não utilizados
- Não remova código morto pré-existente a menos que solicitado

**O teste:** Toda linha alterada deve estar diretamente ligada à solicitação do usuário.

### 4. Execução Orientada a Objetivos

**Defina critérios de sucesso. Itere até verificar.**

Transforme tarefas imperativas em objetivos verificáveis:

| Em vez de... | Transforme em... |
|--------------|------------------|
| "Adicionar validação" | "Escrever testes para entradas inválidas, depois fazê-los passar" |
| "Corrigir o bug" | "Escrever um teste que o reproduza, depois fazê-lo passar" |
| "Refatorar X" | "Garantir que os testes passem antes e depois" |

Para tarefas de múltiplas etapas, apresente um plano breve:

```
1. [Etapa] → verificar: [verificação]
2. [Etapa] → verificar: [verificação]
3. [Etapa] → verificar: [verificação]
```

Critérios de sucesso fortes permitem que o LLM itere de forma autônoma. Critérios fracos ("faça funcionar") exigem esclarecimento constante.

## Instalação

**Opção A: Plugin do Claude Code (recomendado)**

Dentro do Claude Code, primeiro adicione o marketplace:
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

Depois instale o plugin:
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

Isso instala as diretrizes como um plugin do Claude Code, tornando a habilidade disponível em todos os seus projetos.

**Opção B: CLAUDE.md (por projeto)**

Novo projeto:
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

Projeto existente (adicionar ao final):
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

## Uso com Cursor

Este repositório inclui uma regra de projeto do Cursor ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)) para que as mesmas diretrizes se apliquem ao abrir o projeto no Cursor. Veja **[CURSOR.md](CURSOR.md)** para configuração, uso da regra em outros projetos e como isso se relaciona com o Claude Code.

## Percepção Fundamental

De Andrej:

> "LLMs são excepcionalmente bons em iterar até atingirem objetivos específicos... Não diga a eles o que fazer, dê critérios de sucesso e observe-os agirem."

O princípio "Execução Orientada a Objetivos" captura isso: transforme instruções imperativas em objetivos declarativos com laços de verificação.

## Como Saber se Está Funcionando

Estas diretrizes estão funcionando se você notar:

- **Menos alterações desnecessárias nos diffs** — Apenas as alterações solicitadas aparecem
- **Menos reescritas por supercomplicação** — O código é simples logo de início
- **Perguntas de esclarecimento surgem antes da implementação** — Não após os erros
- **PRs limpos e mínimos** — Sem refatorações passageiras ou "melhorias"

## Personalização

Estas diretrizes foram projetadas para serem mescladas com instruções específicas do projeto. Adicione-as ao seu `CLAUDE.md` existente ou crie um novo.

Para regras específicas do projeto, adicione seções como:

```markdown
## Diretrizes Específicas do Projeto

- Use o modo estrito do TypeScript
- Todos os endpoints da API devem ter testes
- Siga os padrões existentes de tratamento de erros em `src/utils/errors.ts`
```

## Nota sobre Tradeoffs

Estas diretrizes priorizam **cautela em detrimento de velocidade**. Para tarefas triviais (correções simples de erros de digitação, one-liners óbvios), use bom senso — nem toda alteração precisa do rigor completo.

O objetivo é reduzir erros custosos em trabalho não trivial, não desacelerar tarefas simples.

## Licença

MIT
