# SPEC.md — Contrato de saída do RESUME

> **Normativo.** O que está fechado vem sem marca; lacuna conhecida é marcada com ⛔
> e o que a bloqueia. Decisões em [docs/decisoes.md](docs/decisoes.md); ameaças e
> controles em [SECURITY.md](SECURITY.md); exemplos executáveis em
> [docs/exemplos.md](docs/exemplos.md).
>
> Nomes: repositório `skill-RESUME` · skill `resume` · comando `/resume`.
>
> **A fonte da verdade do comportamento é [`skills/resume/SKILL.md`](skills/resume/SKILL.md)**
> — é aquele arquivo que o modelo carrega. Esta SPEC existe para que uma divergência
> entre o que a skill manda e o que a documentação promete seja **verificável**, e
> não uma questão de leitura. Divergiu: a SKILL.md está certa e a SPEC está com
> defeito, ou o contrário — mas uma das duas está errada, e a entrega não fecha
> enquanto as duas não disserem a mesma coisa.

---

## 1. O esqueleto (ordem obrigatória)

Toda resposta emitida no chat, com a skill ligada, tem exatamente esta ordem:

```
1. <LINHA DE STATUS>
2. <comentários numerados>
3. O QUE PRECISA SER FEITO:
4. PRÓXIMA AÇÃO:
```

### 1.1 Linha de status

**Sempre a linha 1.** Antes dos comentários, antes de qualquer prosa, antes de
qualquer narração de ferramenta.

| Status | Quando | Forma |
|---|---|---|
| `CONCLUÍDO` | Tudo que o leitor pediu está **feito e verificado**. | `CONCLUÍDO` ou `CONCLUÍDO — <o que agora funciona>` |
| `EM ANDAMENTO` | Há trabalho em curso e algo restante. | `EM ANDAMENTO — etapa 3 de 7: <título>` |
| `BLOQUEADO` | Não dá para prosseguir sem decisão, credencial, acesso ou resposta. | `BLOQUEADO — <a única coisa que destrava>` |

`CONCLUÍDO` é **afirmação sobre a realidade**, não jeito de encerrar mensagem.
Proibido em trabalho parcial, em trabalho não verificado e em "pronto, exceto".
Teste que falha não é `CONCLUÍDO`; etapa pulada não é `CONCLUÍDO`. Na dúvida:
`EM ANDAMENTO`, com o motivo nos comentários. (Ameaça T-01.)

### 1.2 Comentários

- **Todo comentário é numerado** (`1.`, `2.`, `3.`) — nunca bullet, nunca parágrafo
  solto. O número é o que permite ao leitor apontar: "explica o 3", "refaz o 2.1".
- **Assuntos ganham o número de topo** quando a resposta cobre mais de um assunto,
  ou quando o contexto acumulado ficou grande demais para o leitor segurar. Os
  comentários passam a aninhar (`2.1`, `2.2`).
- **O número do assunto é estável na sessão** (ADR-006): assunto 3 no turno 4
  continua sendo o assunto 3 no turno 20. Renumerar destrói a única função do
  número.
- **Profundidade sem teto** (ADR-005): `2.1.3.1` é legítimo quando o material é
  assim. Proibido achatar hierarquia real para caber num orçamento de profundidade,
  e proibido fabricar profundidade que não existe.

### 1.3 `O QUE PRECISA SER FEITO:`

- Vem **depois** dos comentários.
- Enumerado, **sem teto de itens** (ADR-005). Doze achados = doze itens. Ranquear é
  bem-vindo; truncar não é. Proibido "e outros".
- Uma ação delimitada por item.
- Cada item carrega estado: `[x]` feito · `[ ]` pendente · `[!]` bloqueado.
- **A seção permanece quando o status é `CONCLUÍDO`**: todos os itens em `[x]`, e
  essa checklist fechada **é** o registro do que foi entregue.
- Nada rastreado (resposta puramente informativa) → colapsa em uma linha:
  `O QUE PRECISA SER FEITO: nada pendente.`
- O cabeçalho **nunca some**.

### 1.4 `PRÓXIMA AÇÃO:`

- Vem **por último**.
- **Uma** recomendação, concreta o bastante para começar agora. É recomendação:
  diga o porquê na mesma frase quando o motivo não for óbvio.
- Dois caminhos genuinamente abertos → o recomendado primeiro, a alternativa
  depois. Proibido entregar cardápio sem ranque.
- **Status `CONCLUÍDO` → a seção não existe** (ADR-002). Não é vazia, não é "nada":
  é ausente.

### 1.5 Colapso e ausência

| Seção | Sem conteúdo | Sob `CONCLUÍDO` |
|---|---|---|
| Linha de status | impossível — sempre existe | `CONCLUÍDO` |
| Comentários | impossível — sempre existe | permanecem |
| `O QUE PRECISA SER FEITO:` | colapsa em 1 linha, cabeçalho fica | permanece, tudo `[x]` |
| `PRÓXIMA AÇÃO:` | colapsa em 1 linha, cabeçalho fica | **ausente** |

---

## 2. Marcadores por idioma

O idioma da resposta é o **idioma em que o leitor escreve** — os marcadores saem
nesse idioma (ADR-004).

| | pt-BR (canônico) | English (canônico) |
|---|---|---|
| Status — concluído | `CONCLUÍDO` | `DONE` |
| Status — em curso | `EM ANDAMENTO — etapa 3 de 7: <título>` | `IN PROGRESS — step 3 of 7: <title>` |
| Status — bloqueado | `BLOQUEADO — <motivo>` | `BLOCKED — <reason>` |
| Seção de pendências | `O QUE PRECISA SER FEITO:` | `WHAT NEEDS TO BE DONE:` |
| Seção de próxima ação | `PRÓXIMA AÇÃO:` | `NEXT ACTION:` |

Outros idiomas: tradução **literal** dos cinco marcadores, **idêntica durante toda a
sessão** — marcador que muda de redação entre turnos deixou de ser marcador.
Proibido misturar dois idiomas na mesma resposta.

---

## 3. Preservação literal (vale em todos os níveis)

Nunca comprimido, nunca parafraseado, nunca truncado em silêncio — inclusive em
`ultra`:

- blocos de código e diffs, exatamente como são;
- comandos, caminhos de arquivo, número de linha e de coluna, exatos;
- strings de erro, stack traces e linhas de log, exatos — inclusive a parte que
  parece ruído, que é normalmente onde está a causa;
- identificadores, nomes de API, flags e números de versão, exatos.

Saída genuinamente enorme **pode** ser elidida, desde que **rotulada e contada**:

```
[… 240 linhas omitidas — trace completo em build.log …]
```

Truncar erro em silêncio é a falha que esta skill existe para impedir (T-02).

---

## 4. Compressão

### 4.1 Estado inicial

**Desligada** (ADR-001). Com a skill ligada e a compressão desligada, o que muda é
só o **formato**: frase completa, explicação completa, prosa comum dentro das
seções. Economia de token é **consequência** do esqueleto, nunca meta.

### 4.2 Comandos

| Comando | Efeito |
|---|---|
| `/resume` | Skill ligada, **compressão desligada**. Só o esqueleto. |
| `/resume compress on` | Compressão ligada no nível **`normal`**. |
| `/resume compress off` | Compressão desligada. Esqueleto permanece. |
| `/resume lite` \| `normal` \| `full` \| `ultra` | Skill ligada, compressão no nível nomeado. |
| `/resume off` | Skill desligada. |

Também desligam: "stop resume", "parar resume".

⚠️ **Colisão declarada:** `normal` é **nível de compressão**, não o desligamento.
"Modo normal" desliga o caveman, mas aqui não desliga nada. Entrada ambígua do
leitor → uma linha perguntando, nunca adivinhação (ADR-003).

### 4.3 Os quatro níveis

Ordem de intensidade: `lite` → `normal` → `full` → `ultra`.

| Nível | O que remove |
|---|---|
| **lite** | Gentileza, preâmbulo, recapitulação, fecho ("espero que ajude", "qualquer coisa avise") e hedge que não carrega incerteza real. Artigo, frase completa e gramática normal **ficam**. |
| **normal** | Tudo de `lite`, mais: sinônimo curto no lugar do longo, sem narração de chamada de ferramenta, sem tabela decorativa nem emoji, cada fato dito uma vez. Frases seguem gramaticais. |
| **full** | Tudo de `normal`, mais: artigos caem, fragmento é permitido, prosa telegráfica. Termo técnico continua exato. |
| **ultra** | Tudo de `full`, mais: conjunção cai quando causa-e-efeito continua inequívoco, uma palavra onde uma palavra basta. |

Ligar sem nomear nível → **`normal`**.

Os seis níveis do caveman viraram quatro: os três `wenyan-*` (chinês clássico) são
um produto diferente, não um nível de compressão (ADR-008).

### 4.4 Invariantes de compressão

Valem em **todos** os níveis:

1. **Compressão só encurta.** Proibido **acrescentar** palavra para soar comprimido.
   Nada de pronome ou cópula inserido para fingir gramática quebrada — "quando ele
   não" custa um token **a mais** que "quando não" e diz o mesmo. Se a forma
   comprimida não for mais curta que a forma comum, use a comum.
2. **Proibido inventar abreviação** (`cfg`, `impl`, `req`, `res`, `fn`, `auth`): o
   tokenizador quebra igual à palavra inteira — zero token economizado e o leitor
   ainda tem de decodificar. Sigla consagrada (DB, API, HTTP) é permitida.
3. **Proibida seta causal** (`→`): token próprio, economia zero, custo de clareza.
4. **Nunca derrubar `não`, `nunca`, `só`, `exceto`.** Inverter sentido é pior que
   qualquer token economizado.
5. **Número, unidade, versão e identificador exatos.**
6. **Idioma do leitor preservado.** Comprime-se o estilo, não o idioma. "Derrubar
   artigo" vale só para idioma com artigo; onde marcador pequeno carrega caso ou
   papel (partícula, posposição) ele é gramática, não enrolação — mantenha e
   comprima a polidez.
7. **O esqueleto (§1) e a preservação literal (§3) são intocáveis** — em `ultra`
   iguais a em `off`.

### 4.5 Auto-clareza (a compressão se suspende sozinha)

Cai para prosa comum — **o esqueleto fica** — quando:

1. há aviso de risco de segurança;
2. há confirmação de ação irreversível ou destrutiva (`rm -rf`, force push,
   migração de schema, drop de tabela);
3. a própria compressão criaria ambiguidade técnica (sequência em que conjunção
   derrubada deixa a ordem legível de duas formas);
4. o leitor pede para esclarecer, ou repete a pergunta;
5. o leitor pede "explica" / "me mostra passo a passo" — a explicação corre o quanto
   o assunto exigir; numeração e cabeçalho continuam valendo, e são eles que tornam
   uma explicação longa navegável.

Retomar o nível depois, **sem anunciar**.

---

## 5. Persistência e fronteiras

### 5.1 Persistência

As regras valem para **toda resposta** até o desligamento explícito. Não expiram
por número de turnos e não caem quando o assunto muda. Na dúvida sobre estar
valendo: está. O nível de compressão persiste do mesmo jeito.

### 5.2 Fronteiras

O esqueleto é **contrato de saída de chat**. O que é persistido fora da conversa sai
em prosa comum, sem linha de status e sem marcador:

- código e comentário de código;
- mensagem de commit;
- documentação;
- texto de issue / PR / ticket / relatório de defeito;
- arquivo de memória;
- qualquer mensagem endereçada a terceiro.

Leitor pergunta em que modo você está → responda direto.

---

## 6. Instalação e empacotamento

| Alvo | Caminho |
|---|---|
| Fonte da verdade | [`skills/resume/SKILL.md`](skills/resume/SKILL.md) |
| Plugin Claude Code | [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) |
| Marketplace | [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) |
| Instalação manual | [`INSTALL.md`](INSTALL.md) |

**Cópia única, de propósito:** existe **um** `SKILL.md` no repositório. Nem cópia em
`.claude/skills/`, nem symlink — cópia diverge em silêncio e symlink não sobrevive a
clone em Windows com a configuração padrão do git (ADR-007 do skill-AUDITOR ensinou
o primeiro; o segundo é limitação de plataforma). Quem instala copia; quem
desenvolve edita a fonte.

---

## 7. Lacunas conhecidas

- ⛔ **Nenhuma verificação automatizada de cumprimento.** A skill é contrato de
  prompt: quem cumpre é o modelo. Não existe hook, script nem eval que falhe quando
  o esqueleto não sai. É a fase **F2** ([`.continue/escopo-projeto.md`](.continue/escopo-projeto.md))
  e a ameaça **T-01** ([`SECURITY.md`](SECURITY.md)).
- ⛔ **Divergência SPEC × SKILL.md não é checada por máquina.** Hoje depende de quem
  edita lembrar de editar os dois. Idem F2.
- ⛔ **Sem medição.** Não há número medido de economia de token por nível — e, por
  desenho, não haverá alegação de economia sem medição (T-05).
- ⛔ **Marcadores canônicos só em pt-BR e EN.** Outro idioma cai na regra de tradução
  literal do §2, sem conjunto revisado.
