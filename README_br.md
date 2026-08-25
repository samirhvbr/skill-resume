# skill-RESUME

**Toda resposta no mesmo formato: onde estamos, o que falta, o que fazer agora.**

Skill para o Claude Code (e qualquer harness que carregue um `SKILL.md`) que fixa a
*estrutura* da resposta em vez do tamanho dela. A linha 1 sempre diz o status. Os
comentários são sempre numerados. As duas últimas seções são sempre o que falta e o
que fazer agora.

Economia de token aqui é **consequência**, nunca meta — a remoção de enrolação é
opcional e **nasce desligada**.

> 🇺🇸 **English version:** [README.md](README.md) — o README principal é a vitrine em
> inglês; **toda a documentação do projeto é em português (pt-BR) por convenção**,
> com exceção do `README.md`, do `INSTALL.md` e da própria `SKILL.md`, que são a
> superfície voltada para fora.
>
> **A skill:** [skills/resume/SKILL.md](skills/resume/SKILL.md) (o produto — é este
> arquivo que o modelo carrega) · [INSTALL.md](INSTALL.md).
>
> **Documentação:** [CLAUDE.md](CLAUDE.md) / [AGENTS.md](AGENTS.md) (regras de quem
> desenvolve este repo) · [SECURITY.md](SECURITY.md) (modelo de ameaça — **leitura
> obrigatória**) · [SPEC.md](SPEC.md) (contrato de saída normativo) ·
> [docs/README.md](docs/README.md) (índice técnico) ·
> [docs/decisoes.md](docs/decisoes.md) (ADRs) ·
> [docs/comparativo.md](docs/comparativo.md) (de onde isto veio) ·
> [docs/exemplos.md](docs/exemplos.md) (exemplos normativos) ·
> [version.md](version.md) (versão e formato de commit) ·
> [.continue/estado-atual.md](.continue/estado-atual.md) (onde o projeto está).
>
> Irmão do [skill-AUDITOR](https://github.com/samirhvbr/skill-AUDITOR) e do
> [skill-COMMITTER](https://github.com/samirhvbr/skill-COMMITTER) — mesmo padrão de
> documentação e mesma regra de aceite: **controle só conta com teste que falha
> quando o controle é desligado**.

## O formato

```
<LINHA DE STATUS>                ← sempre a linha 1

<comentários numerados — a substância>

O QUE PRECISA SER FEITO:
1. [x] ...
2. [ ] ...

PRÓXIMA AÇÃO:
<uma recomendação de próximo passo>
```

| Status | Quando |
|---|---|
| `CONCLUÍDO` | Tudo que foi pedido está feito **e verificado**. A `PRÓXIMA AÇÃO:` desaparece por completo. |
| `EM ANDAMENTO — etapa 3 de 7: <título>` | Há trabalho em curso e algo restante. |
| `BLOQUEADO — <motivo>` | Não dá para prosseguir sem decisão, credencial ou resposta. |

`CONCLUÍDO` é **afirmação sobre a realidade**, não jeito de encerrar mensagem. Teste
que falha não é `CONCLUÍDO`. Etapa pulada não é `CONCLUÍDO`. Na dúvida, o status é
`EM ANDAMENTO`.

## O que muda

**Antes**

> Ótima pergunta! Deixa eu dar uma olhada. Seu fluxo de autenticação tem algumas
> peças móveis — o middleware, a verificação do token e o tratamento do cookie.
> Olhando o `src/auth.ts`, a função `verifyToken` parece estar checando as coisas na
> ordem errada. Uma abordagem seria inverter as condições. Depois disso, você
> provavelmente vai querer rodar os testes de autenticação. A propósito, talvez
> valha olhar as versões das suas dependências também. Espero que ajude! Qualquer
> coisa é só avisar.

**Depois**

```
EM ANDAMENTO — etapa 2 de 4: corrigir a verificação de token

1. `verifyToken` rejeita o token antes de checar a expiração — `src/auth.ts:42`.
   As duas condições estão invertidas: o `return null` do caminho de assinatura
   inválida roda antes do `if (exp < now)`.
2. O middleware engole a exceção, então o 401 chega ao cliente sem corpo — é por
   isso que o front mostra "erro desconhecido".

O QUE PRECISA SER FEITO:
1. [x] Inverter as condições em `src/auth.ts:42-58`
2. [ ] Propagar a mensagem da exceção em `src/middleware/auth.ts:19`
3. [ ] Rodar `npm test -- auth.spec.ts`
4. [ ] Em separado: versões de dependência desatualizadas — não bloqueia

PRÓXIMA AÇÃO:
Propagar a mensagem no middleware (item 2) antes de mexer em qualquer outra coisa
— sem ela, se o item 3 falhar você depura um 401 sem corpo.
```

A tangente não sumiu e não interrompeu: virou o item 4.

## Instalação

```bash
git clone https://github.com/samirhvbr/skill-RESUME.git
cp -r skill-RESUME/skills/resume ~/.claude/skills/resume
```

Depois, em qualquer sessão:

```text
/resume
```

Outros harnesses, instalação por plugin e instalação por projeto:
[INSTALL.md](INSTALL.md).

## Comandos

| Comando | Efeito |
|---|---|
| `/resume` | Ligada, **compressão desligada**. Só o formato. |
| `/resume compress on` | Compressão ligada no nível `normal`. |
| `/resume compress off` | Compressão desligada. O formato continua. |
| `/resume lite` \| `normal` \| `full` \| `ultra` | Ligada, com compressão no nível nomeado. |
| `/resume off` | Desligada. Também: "parar resume", "stop resume". |

> ⚠️ `normal` é **nível de compressão**, não o desligamento — mesmo que "modo normal"
> desligue as duas skills das quais esta descende. Desligar é `/resume off` ou
> "parar resume". A colisão é deliberada e está documentada, não é descuido
> ([ADR-003](docs/decisoes.md)).

## A remoção de enrolação é opcional — e nasce desligada

Fora da caixa, esta skill muda o **formato** da resposta e nada mais: frase completa,
explicação completa, prosa comum dentro das seções. **Você precisa ligar a
compressão.** É a decisão que separa esta skill das duas que ela mescla: a estrutura
é o produto, e uma contagem menor de tokens é efeito colateral disso — não um alvo a
perseguir.

Ligada, são quatro níveis, do mais leve ao mais pesado:

| Nível | O que remove |
|---|---|
| `lite` | Gentileza, preâmbulo, recapitulação, fecho de cortesia e hedge vazio. Artigo, frase completa e gramática normal ficam. |
| `normal` *(default ao ligar)* | `lite` + sinônimo curto no lugar do longo, sem narração de chamada de ferramenta, sem tabela decorativa nem emoji, cada fato dito uma vez. |
| `full` | `normal` + artigos caem, fragmento permitido, prosa telegráfica. |
| `ultra` | `full` + conjunção cai quando causa-e-efeito continua inequívoco, uma palavra onde uma palavra basta. |

**O formato nunca é comprimido.** Em `ultra`, a linha de status, a numeração e os
cabeçalhos são exatamente o que são em `off`. A compressão age na prosa **dentro**
das seções.

A compressão também **se suspende sozinha** — sem ninguém pedir — em aviso de
segurança, confirmação de ação irreversível e em qualquer caso onde derrubar
palavras deixaria o sentido legível de duas formas.

## O que ela nunca faz

- **Nunca limita lista.** Doze achados = doze itens. Ranquear, sim; truncar, não; "e
  outros", jamais. A lista de pendências é a memória entre turnos, então item
  derrubado é trabalho perdido, e não formatação mais limpa.
- **Nunca limita profundidade.** `2.1.3.1` é legítimo quando o material é assim.
- **Nunca toca em código nem em texto de erro.** Código, diff, comando, caminho,
  número de linha, stack trace, log e identificador saem **verbatim em todos os
  níveis**. Elisão existe, mas só rotulada e contada: `[… 240 linhas omitidas …]`.
- **Nunca renumera assunto.** Assunto 3 no turno 4 continua sendo o 3 no turno 20.
- **Nunca vaza o formato para fora do chat.** Mensagem de commit, issue, PR,
  documentação e mensagem a terceiro saem em prosa comum, sem linha de status.

## De onde isto veio

Merge de duas skills, usadas lado a lado até a sobreposição ficar evidente:

- 🪨 [**caveman**](https://github.com/JuliusBrussee/caveman), de Julius Brussee — os
  níveis de compressão e os invariantes que impedem a compressão de virar mutilação
  (nunca **acrescentar** palavra para soar comprimido, nunca inventar abreviação,
  nunca derrubar uma negação).
- 🧠 [**i-have-adhd**](https://github.com/ayghri/i-have-adhd), de Ayoub Ghriss —
  etapas numeradas, restabelecer o estado a cada turno, uma próxima ação concreta,
  sem preâmbulo e sem fecho de cortesia.

O comparativo completo — incluindo as cinco coisas que o RESUME **recusou** herdar, e
por quê — está em [docs/comparativo.md](docs/comparativo.md). Nenhum arquivo foi
copiado de nenhum dos dois projetos; o parentesco é de regras, e está creditado de
propósito.

## Status

**Versão `0.1.0` — a skill está escrita e instalável.**

Limite declarado: isto é um **contrato de prompt**. Nada falha quando o formato não
sai — não há hook, não há script, não há eval. As duas skills de origem têm o mesmo
limite; a diferença aqui é que ele está escrito ([SECURITY.md](SECURITY.md),
[SPEC.md](SPEC.md) §7) e virou a próxima fase de trabalho, em vez de ficar como
premissa não dita.

Nenhuma porcentagem de economia de token é alegada em lugar nenhum deste
repositório, porque nada foi medido ainda. É deliberado: sem benchmark que produza o
número, o número não entra.

## Licença

MIT — ver [LICENSE](LICENSE).
