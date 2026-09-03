# skill-RESUME — Guia do Agente (AGENTS.md)

> **Leia também:** [README.md](README.md) / [README_br.md](README_br.md) (o produto) ·
> [SECURITY.md](SECURITY.md) (**leitura obrigatória** — modelo de ameaça) ·
> [SPEC.md](SPEC.md) (contrato de saída normativo) ·
> [docs/decisoes.md](docs/decisoes.md) (ADR-001 a ADR-009 + pendências) ·
> [docs/comparativo.md](docs/comparativo.md) (de onde o projeto veio) ·
> [docs/exemplos.md](docs/exemplos.md) (normativo por exemplo) ·
> [version.md](version.md) (versão + formato de commit).
>
> `CLAUDE.md` e `AGENTS.md` são **espelhados** abaixo do H1 — editar os dois.

---

## 🔄 Antes de começar: `git pull`

**SEMPRE** verifique atualizações remotas antes de escrever ou alterar qualquer
coisa neste repositório:

```bash
git pull
```

---

## O que é este repo

Skill **resume** (comando `/resume`): contrato de **formato de saída**. Toda resposta
sai com linha de status na linha 1 (`CONCLUÍDO` / `EM ANDAMENTO — etapa X de Y` /
`BLOQUEADO`), comentários numerados, `O QUE PRECISA SER FEITO:` e `PRÓXIMA AÇÃO:`.

Nasceu do **merge** de duas skills de terceiros que o Samir usava em paralelo —
[caveman](https://github.com/JuliusBrussee/caveman) e
[i-have-adhd](https://github.com/ayghri/i-have-adhd) — com as divergências resolvidas
a favor da **estrutura**. Os dois estão clonados em `~/x/SKILL/caveman` e
`~/x/SKILL/i-have-adhd`: **leitura de referência, nunca fonte de cópia** (ADR-009).

Irmão do **skill-AUDITOR** e do **skill-COMMITTER** (`~/x/SKILL/`): mesmo padrão de
documentação e mesma regra de aceite.

---

## ⚠️ Estado do projeto: F1 entregue, sem verificação

O que **existe**: [`skills/resume/SKILL.md`](skills/resume/SKILL.md) (o produto
inteiro — um arquivo), a `SPEC.md`, o `SECURITY.md` com T-01 a T-07, nove ADRs, o
comparativo de origem e os exemplos normativos.

O que **não existe**: hook, script, eval, benchmark. **Nada falha quando o esqueleto
não sai.** Por isso nenhum controle do `SECURITY.md` está em `✅` — pela regra de
aceite da casa, os sete estão em `[x]` (escrito) e nenhum em `[x] ✅` (provado). É a
fase **F2**.

Ao trabalhar aqui:

- **Não descreva como pronto** o que é spec. `SPEC.md` §7 lista as lacunas com ⛔.
- **Não confunda "escrito" com "implementado"** — regra herdada do AUDITOR: controle
  só conta com teste que **falha quando o controle é desligado**.
- **Não alegue número de economia de token.** Sem benchmark que produza o número, o
  número não entra em lugar nenhum (T-05). Vale inclusive para "cerca de", "~" e
  "reduz bastante".
- **Não feche pendência dentro de um how-to** — decisão nova vira ADR em
  [docs/decisoes.md](docs/decisoes.md).

---

## A regra mais fácil de quebrar aqui

**`skills/resume/SKILL.md` e `SPEC.md` precisam dizer a mesma coisa, e nada verifica
isso.**

A `SKILL.md` é a fonte da verdade do **comportamento** (é ela que o modelo carrega).
A `SPEC.md` é a fonte da verdade do **contrato** (é ela que a documentação cita).
Editar uma sem a outra produz divergência silenciosa — o defeito exato que o
skill-COMMITTER pagou para aprender na `0.5.0` dele (prompt e validador discordando
do limite de caracteres, queimando teto de fallback sem ninguém entender por quê).

Mexeu numa, mexeu na outra, **no mesmo commit**. E se um exemplo do
[docs/exemplos.md](docs/exemplos.md) deixar de valer, ele também entra no mesmo
commit.

---

## Padrão de Commits (obrigatório)

Formato: `X.Y.Z - Descrição curta em português`. A versão **sempre** vem de
[`version.md`](version.md), bumpada **no mesmo commit**. Critério resumido: **Z** =
entrega que muda comportamento/regra/exemplo normativo; **Y** = mudança no esqueleto,
na escada de compressão, fase concluída ou ADR que muda direção; **X** = skill
estável operando. **Proibido** `feat:`/`fix:`/`chore:` e mensagens vagas.

> Este repositório **não tem `.committer.yml`** — nasceu sem, de propósito (P-02).
> Enquanto não tiver, **commit e push são trabalho do agente**, no fluxo manual da
> casa: validar → bumpar `version.md` com entrada de changelog → commit → push.

---

## Regras do produto (não relitigar sem ADR)

1. Compressão é **opcional e nasce desligada**; estrutura é o produto, token é
   consequência (ADR-001).
2. `PRÓXIMA AÇÃO:` **some** sob `CONCLUÍDO`; os outros cabeçalhos **nunca** somem —
   seção vazia colapsa em uma linha (ADR-002).
3. `normal` é **nível**, não desligamento; desligar é `/resume off`. Entrada ambígua
   → uma linha de pergunta (ADR-003).
4. Marcadores no **idioma do leitor**, canônicos em pt-BR e EN, tradução estável por
   sessão nos demais (ADR-004).
5. **Sem teto** de itens de lista e de profundidade de aninhamento (ADR-005).
6. Assunto numerado com **número estável na sessão** (ADR-006).
7. Código, comando e erro **verbatim em todos os níveis**; elisão só rotulada e
   contada (ADR-007).
8. **Quatro** níveis (`lite`, `normal`, `full`, `ultra`), `normal` ao ligar sem
   nomear; `wenyan-*` fora (ADR-008).
9. Repositório público, MIT, **atribuição nominal** às duas origens — sumir com os
   créditos é regressão, não simplificação (ADR-009).

E o que a skill **nunca** faz: truncar lista, encurtar erro, comprimir o esqueleto,
renumerar assunto, alegar `CONCLUÍDO` sem verificar, vazar o formato para commit,
issue, PR, documentação ou mensagem a terceiro.

---

## Regras de escrita

- **Idioma do repositório: pt-BR.** Exceções, todas de vitrine: **`README.md`**,
  **`INSTALL.md`** e **`skills/resume/SKILL.md`** saem em **inglês** — o repositório é
  público e a skill é carregada pelo modelo. Todo o resto (SPEC, SECURITY, docs/,
  .continue/, version.md, este arquivo) é pt-BR.
- Documentação durável → `docs/`. Notas de trabalho → `.continue/`. Contrato
  normativo → `SPEC.md`. O produto → `skills/resume/SKILL.md`.
- Documento novo em `docs/` entra no [índice](docs/README.md) no mesmo commit.
- Sem link para arquivo inexistente; futuro se descreve em texto.
- Distinguir **fato observado**, **inferência** e **recomendação**.

---

## Como o Claude Code deve operar aqui

- **Planeje antes de editar** (`defaultMode: plan`).
- Mudanças pequenas e atômicas; ao concluir entrega, **atualize `version.md`** (com
  entrada de changelog) e `.continue/estado-atual.md`.
- Decisão pendente bloqueia? Faça o que não depende dela e pergunte — não escolha por
  conta própria.
- **Dogfood é barato e vale:** rodar `/resume` enquanto se trabalha aqui é a única
  forma de descobrir onde o esqueleto atrapalha antes da F2 existir.
- **Não copie texto do caveman nem do i-have-adhd.** O parentesco é de regras e está
  creditado; cópia de arquivo mudaria a situação de licença (ADR-009).

---

## Referências rápidas

- O produto: [skills/resume/SKILL.md](skills/resume/SKILL.md)
- Versão e commits: [version.md](version.md)
- Modelo de ameaça: [SECURITY.md](SECURITY.md)
- Contrato de saída: [SPEC.md](SPEC.md)
- Decisões: [docs/decisoes.md](docs/decisoes.md)
- Origem do projeto: [docs/comparativo.md](docs/comparativo.md)
- Exemplos: [docs/exemplos.md](docs/exemplos.md)
- Escopo e fases: [.continue/escopo-projeto.md](.continue/escopo-projeto.md)
- Estado atual: [.continue/estado-atual.md](.continue/estado-atual.md)
- Perfil do agente: [.claude/README.md](.claude/README.md)
- Irmãos: `~/x/SKILL/skill-AUDITOR`, `~/x/SKILL/skill-COMMITTER`
- Origens (referência, não cópia): `~/x/SKILL/caveman`, `~/x/SKILL/i-have-adhd`
- Remoto: `github.com/samirhvbr/skill-RESUME` (**público**) · branch `master`

---

<!-- RELEASES-RULE:repodocs -->

## Releases — the `version.md` on GitHub is what the Releases show

> Marked echo. The single source is **[samirhvbr/repodocs](https://github.com/samirhvbr/repodocs/blob/master/docs/versioning.md)**
> — change it there, not here. This block is regenerated.

**The `version.md` of the default branch, on GitHub, is what the GitHub Releases
must show.** The local checkout does not enter the calculation: it can be behind,
ahead or mid-work, and none of that is published — GitHub cannot tag a commit it
does not have.

**The bump and the Release are one act.** A commit that bumps `version.md` is not
finished until that version has a tag, a published Release, and the **`Latest`
badge on it** — the same push, not "later". A badge sitting on an older release
tells whoever looks that the project is at a version it is not.

- `.github/workflows/release.yml` does it on any push that touches `version.md`.
- `./tools/release.sh` does it by hand. It is **idempotent and self-healing**:
  it publishes whatever is missing and moves a drifted badge back. Running it is
  always safe, so it is both the check and the fix.

A PR publishes nothing while it is a PR. The moment it merges, the push moves
`version.md` on the default branch and the Release becomes that version.

Tag and Release title are the **bare version — no `v` prefix**.

## Language — English (US), everywhere in the repository

**Everything that lives in this repository, or in GitHub's interface around it,
is written in English (US)**: documents, **commit messages**, pull request titles
and bodies, issues, code comments, changelog entries, release notes.

Commit format: `X.Y.Z - short description in English`. The version comes from
`version.md` and is bumped in the same commit. Conventional Commits prefixes
(`feat:`, `fix:`, `chore:`) and vague one-word messages are forbidden.

**Exactly one carve-out:** end-user-facing strings — UI text, transactional
email, product copy. That is product i18n for a Brazilian audience, not
repository content.

History is not rewritten: Portuguese messages already in the log stay as they
are.

<!-- /RELEASES-RULE -->
