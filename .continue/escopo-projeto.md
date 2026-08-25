# Escopo e fases — skill-RESUME

> Fases com critério de pronto. Decisões fechadas em
> [`docs/decisoes.md`](../docs/decisoes.md) (ADR-001 a ADR-009); pendências P-01 a
> P-05 na tabela de lá. Alterar fase = atualizar aqui + bumpar `version.md`.

## F0 — Decisão e comparativo ✅ (0.1.0)

O Samir rodava [caveman](https://github.com/JuliusBrussee/caveman) e
[i-have-adhd](https://github.com/ayghri/i-have-adhd) em paralelo e montou a tabela
de diferenças. O RESUME nasce como o **merge** das duas, com as divergências
resolvidas a favor da **estrutura** — não da compressão.

Entregue: [`docs/comparativo.md`](../docs/comparativo.md) (a tabela + o que foi
herdado e o que foi recusado, com o ADR de cada linha).

## F1 — A skill escrita + documentação da casa ✅ (0.1.0)

- ✅ [`skills/resume/SKILL.md`](../skills/resume/SKILL.md) — o produto: esqueleto de
  quatro blocos, três status, marcadores em dois idiomas canônicos, dez regras,
  quatro níveis de compressão desligados por default, fronteiras e pre-send check.
- ✅ [`SPEC.md`](../SPEC.md) — contrato normativo, com as lacunas marcadas em ⛔.
- ✅ [`SECURITY.md`](../SECURITY.md) — T-01 a T-07, **nenhum em ✅** por regra de
  aceite (controle só conta com verificação que falha quando ele é desligado).
- ✅ [`docs/exemplos.md`](../docs/exemplos.md) — normativo por exemplo.
- ✅ Padrão de documentação da casa: `README.md` (EN) + `README_br.md`, `CLAUDE.md` +
  `AGENTS.md` espelhados, `INSTALL.md`, `version.md`, `docs/`, `.continue/`,
  `.claude/`, `.claude-plugin/`, `LICENSE`.

**Critério de pronto atingido:** a skill instala e liga, e toda regra do `SPEC.md`
tem um exemplo em `exemplos.md` ou uma ameaça em `SECURITY.md` atrás dela.

## F2 — Verificação de cumprimento ⛔ (P-01)

A fase que tira o projeto de "contrato de prompt" e o põe em "controle medido". Hoje
**nada falha** quando o esqueleto não sai.

- ⛔ **Divergência SPEC × SKILL.md** — checagem que falhe quando os dois discordam
  (marcador, nível, ordem de bloco). É a mais barata e a que pega mais regressão.
- ⛔ **Suíte de eval** nos moldes do `evals/` do i-have-adhd: casos fixos, rubrica,
  runner. Um caso por ameaça de T-01 a T-07.
- ⛔ **Caso `CONCLUÍDO` × item pendente** — a contradição do T-01 é hoje visível ao
  leitor e a mais nada.
- ⛔ Decidir se existe hook (`Stop`) conferindo a última resposta, ou se a
  verificação é só de repositório.

**Pronto quando:** cada um de T-01 a T-07 tem um caso que **falha com o controle
desligado**, e um `SECURITY.md` sai da coluna `[x]` para `[x] ✅`.

## F3 — Benchmark ⛔ (P-04)

- ⛔ `benchmarks/` com prompts fixos, o baseline sem skill e os quatro níveis.
- ⛔ Número medido de economia por nível — e **só depois disso** a documentação pode
  citar porcentagem (T-05).
- ⛔ Medir também o custo do esqueleto com compressão desligada, que é o caso
  default: o formato **acrescenta** linhas, e o honesto é saber quantas.

**Pronto quando:** existe número reprodutível para os cinco modos (`off`, `lite`,
`normal`, `full`, `ultra`) contra um baseline sem skill.

## F4 — Adoção e publicação ⛔

- ⛔ Uso real do Samir nos repos da casa, com o que quebrar voltando para a `SPEC.md`.
- ⛔ Decidir a publicação como fonte de marketplace (P-05).
- ⛔ Decidir o opt-in do COMMITTER neste repositório (P-02) — nasceu sem, de
  propósito.
- ⛔ Terceiro idioma canônico, se o uso pedir (P-03).

**Pronto quando:** a skill é o modo default do Samir por uma semana sem que ele
precise corrigir o formato à mão.

## Fora de escopo na v1

- **Subagentes e ferramentas próprias** — o caveman tem; aqui o produto é contrato
  de saída, e um subagente não muda o formato de quem responde.
- **Níveis `wenyan-*`** — outro produto, e colide com responder no idioma do leitor
  (ADR-008, ADR-004).
- **Hook que liga a skill sozinha na abertura da sessão** — ligar modo de saída sem
  o leitor pedir é o oposto do ADR-001.
