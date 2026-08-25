# Decisões — skill-RESUME (ADRs)

> Registro cronológico das decisões que **mudam a direção** do projeto. Decisão nova
> entra aqui, com contexto, alternativa descartada e consequência — nunca dentro de
> um how-to. Pendências abertas na tabela do fim.
>
> Contexto de origem: o RESUME nasce do **merge** de duas skills de terceiros que o
> Samir usava em paralelo — [caveman](https://github.com/JuliusBrussee/caveman) e
> [i-have-adhd](https://github.com/ayghri/i-have-adhd). A tabela comparativa que
> originou as decisões está em [comparativo.md](comparativo.md).

---

## ADR-001 — Compressão é opcional e nasce desligada

**Status:** Aceito (2026-08-25)

**Contexto.** O caveman existe para cortar token: a compressão é o produto e está
sempre ligada. O i-have-adhd existe para deixar a resposta acionável: a economia é
efeito colateral. Rodar os dois ao mesmo tempo põe os dois objetivos para disputar a
mesma frase, e quem perde é sempre a clareza — porque a compressão age em toda linha
e a estrutura age só nas bordas.

**Decisão.** No RESUME o produto é a **estrutura**. Economia de token é
**consequência**, nunca meta. A remoção de enrolação é um **opcional que nasce
desligado**, ligado explicitamente pelo leitor.

**Alternativas descartadas.**
- *Compressão ligada por default, como no caveman* — faria o RESUME ser um caveman
  com cabeçalho, e o primeiro conflito entre "cortar" e "explicar" seria resolvido a
  favor de cortar.
- *Sem compressão nenhuma* — jogaria fora a metade do caveman que funciona bem:
  preâmbulo, recapitulação e fecho de cortesia não carregam informação.

**Consequência.** Instalar o RESUME não muda o comprimento das respostas, só a forma.
Quem quiser o corte pede. A documentação **precisa** deixar isso explícito, porque a
expectativa herdada das duas skills de origem é a oposta — é por isso que o
`README.md`, o `README_br.md` e o `INSTALL.md` repetem "off by default".

---

## ADR-002 — `PRÓXIMA AÇÃO:` desaparece sob `CONCLUÍDO`; os outros cabeçalhos, não

**Status:** Aceito (2026-08-25)

**Contexto.** Terminou o trabalho, não há próxima ação. Manter a seção obriga a
preenchê-la, e seção que precisa ser preenchida é preenchida — com trabalho
inventado ("talvez você queira também…"), que é exatamente o padrão que as duas
skills de origem proíbem no fecho.

**Decisão.** Status `CONCLUÍDO` → `PRÓXIMA AÇÃO:` **não existe**. Não é vazia, não é
"nada": é ausente. Os demais cabeçalhos **nunca somem**; seção sem conteúdo colapsa
em uma linha (`O QUE PRECISA SER FEITO: nada pendente.`).

**Por que assimétrico.** As duas metades do esqueleto têm função diferente. O
cabeçalho fixo é **âncora visual** — o olho aprende onde cair, e âncora que às vezes
some deixa de ser âncora. A `PRÓXIMA AÇÃO:` é **conteúdo**, e conteúdo obrigatório
sem matéria-prima vira invenção.

**Consequência.** `O QUE PRECISA SER FEITO:` sobrevive ao `CONCLUÍDO` com todos os
itens em `[x]` — e essa checklist fechada passa a ser o registro do que foi
entregue, que é a resposta do RESUME para "make completed work visible" do
i-have-adhd sem precisar de uma seção de recapitulação.

---

## ADR-003 — `normal` é nível de compressão, e o desligamento é explícito

**Status:** Aceito (2026-08-25)

**Contexto.** O caveman e o i-have-adhd desligam com "normal mode". A escada de
compressão do RESUME tem um nível chamado `normal`. As duas convenções colidem: o
leitor que vem do caveman escreve "normal mode" esperando desligar.

**Decisão.** Desligar é `/resume off`, "stop resume" ou "parar resume". `normal` é
nível. A colisão é **declarada** na SKILL.md e na SPEC §4.2, e entrada ambígua vira
**uma linha de pergunta**, nunca adivinhação.

**Alternativas descartadas.**
- *Renomear o nível* (`padrão`, `médio`, `mid`) — o Samir nomeou os quatro níveis;
  renomear por causa de convenção de outra skill inverte a prioridade.
- *Aceitar "normal mode" como desligamento* — deixaria `/resume normal` e "normal
  mode" com efeitos opostos, que é pior que a colisão original.

**Consequência.** Um caso de ambiguidade permanece por desenho, com a mitigação
sendo perguntar. Registrado para que ninguém "conserte" isso sem ler o motivo.

---

## ADR-004 — Marcadores seguem o idioma do leitor

**Status:** Aceito (2026-08-25)

**Contexto.** Os marcadores nasceram em português (`CONCLUÍDO`, `O QUE PRECISA SER
FEITO:`, `PRÓXIMA AÇÃO:`). O repositório é **público**. Marcador fixo em pt-BR numa
conversa em inglês é ruído; marcador fixo em inglês numa conversa em português
contraria a regra da casa e as duas skills de origem, que preservam o idioma do
usuário.

**Decisão.** Os cinco marcadores saem no **idioma em que o leitor escreve**.
Conjuntos canônicos revisados em **pt-BR** e **EN**, na SKILL.md e na SPEC §2. Outros
idiomas: tradução literal, **estável durante toda a sessão**.

**Consequência.** Ferramenta que queira dar `grep` na saída precisa conhecer o par
do idioma, não uma string só. Aceito: a saída é para humano, e um marcador em idioma
errado custa mais atenção do que custa a um script conhecer duas strings.
Instabilidade de redação entre turnos é o que **quebra** o marcador — daí a regra de
tradução fixa por sessão.

---

## ADR-005 — Sem teto: nem de itens de lista, nem de profundidade

**Status:** Aceito (2026-08-25)

**Contexto.** O i-have-adhd limita listas a 5 itens ("cinco ranqueados batem dez sem
ranque"). É boa ergonomia de **leitura** e é perda de estado na **execução**: o
sexto item não vira "depois", vira esquecido. O RESUME usa a mesma lista como
memória entre turnos (regra 7), o que muda a natureza do trade-off.

**Decisão.** Listas **sem teto** e profundidade de aninhamento **sem teto**. Doze
achados = doze itens. Ranquear é bem-vindo; truncar é proibido, e "e outros" também.

**Consequência.** Lista longa fica longa. A mitigação é ordenar (primeiro item = o
que se faz primeiro) e **dizer o que a ordem significa**, não cortar. A regra virou
controle de segurança — **T-06** no [`SECURITY.md`](../SECURITY.md) —, e não item de
formatação.

---

## ADR-006 — Assunto numerado, com número estável na sessão

**Status:** Aceito (2026-08-25)

**Contexto.** "Numerar os assuntos quando o contexto fica muito grande" (pedido do
Samir). Numerar por resposta resolve a leitura daquele turno e não resolve nada
entre turnos: se o "assunto 3" muda de significado a cada resposta, o número vira
enfeite.

**Decisão.** Assunto ganha o número de topo, comentário aninha embaixo (`2.1`), e o
**número do assunto é estável durante toda a sessão**. Assunto 3 no turno 4 continua
sendo o assunto 3 no turno 20. Renumerar é proibido.

**Consequência.** Assunto encerrado **não** libera o número — o histórico da conversa
continua se referindo a ele. Numeração fica esparsa em sessão longa (1, 4, 7 vivos),
e isso é o comportamento correto.

---

## ADR-007 — Código e erro verbatim em todos os níveis (divergência do caveman)

**Status:** Aceito (2026-08-25)

**Contexto.** O caveman manda *"no dumping long raw error logs unless asked, quote
shortest decisive line"* — coerente com o objetivo dele, já que log cru é onde o
token vai embora. Mas "a linha decisiva" é um **julgamento feito antes de saber a
causa**, e quando ele erra o leitor depura outra coisa.

**Decisão.** Código, diff, comando, caminho, linha/coluna, string de erro, stack
trace, log, identificador, flag e versão saem **verbatim em todos os níveis**,
inclusive `ultra`. Elisão existe, mas só **rotulada e contada**
(`[… 240 linhas omitidas — trace completo em build.log …]`).

**Consequência.** O RESUME em `ultra` pode ficar **mais longo** que o caveman em
`full` numa sessão de depuração. É o trade-off aceito, e é a razão de o ADR-001
existir: aqui token é consequência, não meta.

---

## ADR-008 — Quatro níveis de compressão, não seis

**Status:** Aceito (2026-08-25)

**Contexto.** O caveman tem seis níveis: `lite`, `full`, `ultra` e três `wenyan-*`
(chinês clássico). O Samir pediu quatro: `lite`, `normal`, `full`, `ultra`, com
`normal` como default ao ligar.

**Decisão.** Quatro níveis, na ordem `lite` → `normal` → `full` → `ultra`. Ligar sem
nomear cai em `normal`. Os `wenyan-*` ficam de fora: reescrever a resposta em chinês
clássico é **outro produto**, não um nível a mais de compressão — e colidiria de
frente com o ADR-004, que manda responder no idioma do leitor.

**Consequência.** `normal` fica entre `lite` e `full`, o que faz "normal" soar mais
forte que "lite". A escada é monotônica e está tabelada na SPEC §4.3; o nome veio do
pedido e não se renomeia por estética (ver também ADR-003).

---

## ADR-009 — Repositório público, licença MIT, atribuição às skills de origem

**Status:** Aceito (2026-08-25)

**Contexto.** O RESUME é derivado conceitual de duas skills públicas. O i-have-adhd é
MIT; o caveman tem licenciamento duplo com BSL para partes do produto. Nenhum
arquivo dos dois foi copiado para cá — a SKILL.md foi escrita do zero a partir da
tabela comparativa —, mas as **regras** são reconhecivelmente parentes.

**Decisão.** Repositório **público**, branch principal `master` (padrão da casa),
licença **MIT**, e atribuição nominal às duas origens no `README.md`, no
`README_br.md` e em [`comparativo.md`](comparativo.md).

**Consequência.** Atribuição é obrigação permanente: qualquer redação futura do
README que suma com os créditos é regressão, não simplificação.

---

## Pendências

| # | Pendência | Estado | Quem decide |
|---|---|---|---|
| **P-01** | **Como verificar cumprimento.** Hoje o esqueleto é contrato de prompt e nada falha quando ele não sai. Opções em aberto: hook `Stop` que confere a última resposta, suíte de eval nos moldes do `evals/` do i-have-adhd, ou checagem de divergência SPEC × SKILL.md. É a **F2** e a mitigação de T-01 a T-07. | Aberta | Samir + implementação |
| **P-02** | **Opt-in do COMMITTER** (`.committer.yml` na raiz). Todo repo da casa tem; este nasceu sem, de propósito — instalar automação de commit é decisão do dono, não efeito colateral de criar o repositório. | Aberta | Samir |
| **P-03** | **Idiomas canônicos além de pt-BR e EN.** Hoje qualquer outro idioma cai na regra de tradução literal do SPEC §2, sem conjunto revisado. Candidato natural: es. | Aberta | Samir |
| **P-04** | **Benchmark de economia por nível.** Sem ele, nenhum número entra na documentação (T-05). Nos moldes do `benchmarks/` do caveman. | Aberta | Samir + implementação |
| **P-05** | **Publicar no marketplace de plugins.** `.claude-plugin/marketplace.json` existe e aponta para `./`; falta decidir se o repositório vira fonte de marketplace divulgada. | Aberta | Samir |
