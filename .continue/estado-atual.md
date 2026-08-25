# Estado atual — skill-RESUME

- **2026-08-25 — `0.1.0` (F0 + F1):** nasce o repositório. A skill `resume` é o merge
  de [caveman](https://github.com/JuliusBrussee/caveman) e
  [i-have-adhd](https://github.com/ayghri/i-have-adhd), com as divergências
  resolvidas a favor da **estrutura**: economia de token virou consequência
  declarada e a remoção de enrolação virou opcional desligado por default
  (ADR-001). Entregues a `SKILL.md`, a `SPEC.md`, o `SECURITY.md` (T-01 a T-07), os
  nove ADRs, o comparativo de origem, os exemplos normativos e o padrão de
  documentação da casa completo.

---

## Onde o projeto está

**F0 ✅ · F1 ✅ · F2 ⛔ · F3 ⛔ · F4 ⛔.**

A skill **existe e está escrita**. O que ela **não tem** é qualquer coisa que falhe
quando ela não é cumprida: não há hook, não há script, não há eval. O cumprimento
depende do modelo obedecer o prompt — mesma condição das duas skills de origem, com
a diferença de que aqui o limite está escrito (`SECURITY.md`, `SPEC.md` §7) e virou
a fase F2 em vez de ficar implícito.

Por isso **nenhum controle do `SECURITY.md` está em `✅`**. Pela regra de aceite
herdada do skill-AUDITOR — *controle só conta com teste que falha quando o controle
é desligado* — os sete estão em `[x]` (escrito) e nenhum em `[x] ✅` (provado).

## Próximo passo

**F2, começando pelo mais barato:** a checagem de divergência entre `SPEC.md` e
`skills/resume/SKILL.md`. Hoje os dois dizem a mesma coisa porque foram escritos
juntos; na primeira edição de um só, a divergência é silenciosa — e é o tipo de
defeito que o skill-COMMITTER já pagou para aprender (prompt × validador
divergindo sem ninguém notar, `0.5.0` de lá).

Depois: um caso de eval por ameaça, começando por **T-01** (`CONCLUÍDO` saindo com
item `[ ]` pendente na mesma resposta), que é a ameaça central e a mais fácil de
detectar mecanicamente.

## Trabalho barato que sobrou (alto retorno)

- **Usar a skill neste próprio repositório** (dogfood). É a única forma de descobrir
  onde o esqueleto atrapalha antes de a F2 existir. Custo: `/resume`.
- **Terceiro idioma canônico** — a regra de tradução literal do SPEC §2 cobre, mas
  um conjunto revisado em `es` custa dez linhas.

## Precisa do Samir

- **P-02 — opt-in do COMMITTER.** Este repositório **nasceu sem `.committer.yml`**,
  de propósito: instalar automação de commit é decisão do dono do repositório, não
  efeito colateral de criar o repositório. Todos os outros repos da casa têm. Quer
  ligar? É criar o marcador (e, com ele, o bloco PS no `CLAUDE.md`/`AGENTS.md`).
- **P-05 — publicar como fonte de marketplace.** O `.claude-plugin/marketplace.json`
  existe e aponta para `./`; falta decidir se o repositório é divulgado como
  marketplace instalável.
- **Nome do comando.** `/resume` colide, em inglês, com "currículo" e com o sentido
  de "retomar". Nenhum problema em pt-BR, e o nome foi escolhido — registrado aqui
  só para que a colisão não seja descoberta como surpresa depois.

## Achados que valem registro

- **A colisão do `normal`** (ADR-003) foi encontrada escrevendo a documentação, não
  usando a skill: o caveman e o i-have-adhd desligam com "normal mode", e o RESUME
  tem um **nível** chamado `normal`. Ficou declarada, com a mitigação sendo
  perguntar — em vez de renomear o nível que o Samir escolheu.
- **A assimetria do ADR-002** (a `PRÓXIMA AÇÃO:` some sob `CONCLUÍDO`, os outros
  cabeçalhos não) tem motivo diferente para cada metade: cabeçalho é âncora visual e
  âncora que às vezes some deixa de ser âncora; conteúdo obrigatório sem
  matéria-prima vira trabalho inventado.
- **O teto de 5 itens do i-have-adhd virou ameaça, não preferência.** No RESUME a
  lista de pendências é a **memória entre turnos**, então truncar não é escolha de
  formatação: é perda de estado (T-06).

## Contexto de ambiente

- Repositório **público** em `github.com/samirhvbr/skill-RESUME`, branch principal
  `master`. Licença MIT (ADR-009).
- `~/x` tem auto-pusher ("Version X (clean)" + `pull --rebase`): nunca reescrever
  histórico; conferir `git log` antes de assumir entrega registrada.
- Irmãos com o mesmo padrão de documentação: `~/x/SKILL/skill-AUDITOR` e
  `~/x/SKILL/skill-COMMITTER`. As duas skills de origem estão clonadas em
  `~/x/SKILL/caveman` e `~/x/SKILL/i-have-adhd` — **leitura de referência, nunca
  fonte de cópia**.
