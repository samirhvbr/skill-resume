# Versão — skill-RESUME

**Versão atual:** `0.1.1`

> Este arquivo é a **fonte da verdade** da versão do projeto. Qualquer lugar que
> precise exibir ou reportar a versão extrai o **primeiro número semver (`X.Y.Z`)**
> encontrado aqui. Mantenha a linha **"Versão atual"** sempre como a primeira
> ocorrência de um número de versão. Mesma mecânica dos projetos-irmãos
> (skill-AUDITOR, skill-COMMITTER, SHVIA-WEB, SSHVTERM-DESKTOP).

---

## 1. Convenção de Versionamento (`X.Y.Z`)

| Componente | Significado | Como sobe |
|---|---|---|
| **X** | Release estável — skill instalada e operando na casa | Manual |
| **Y** | Mudança estrutural — alteração no esqueleto de saída, nos marcadores, na escada de compressão ou ADR aceito que muda a direção | Manual |
| **Z** | Incremento a cada entrega (ver gatilhos) | A cada entrega |

Enquanto `X` for `0`, o contrato de saída pode quebrar entre versões `0.Y`.

### Gatilhos de bump do `Z`

- Alterar `skills/resume/SKILL.md` de forma que **mude o comportamento** da skill
  (não vale corrigir redação).
- Criar ou alterar documento em `docs/` ou `SPEC.md` que **muda uma regra**.
- Alterar os marcadores de um idioma ou acrescentar um idioma canônico.
- Alterar a definição de um nível de compressão.
- Alterar `.claude/settings.json` (permissões, perfil de modelo) ou os manifestos
  de plugin.
- Adicionar ou alterar exemplos normativos em `docs/exemplos.md`.

### Gatilhos de bump do `Y`

- Fase concluída (ver [`.continue/escopo-projeto.md`](.continue/escopo-projeto.md)).
- Mudança no **esqueleto** (ordem dos blocos, presença/ausência de seção).
- Mudança na escada de compressão (nível novo, nível removido, default alterado).
- ADR novo com status **Aceito** que muda a direção.

> Correções de texto, typo e formatação **não** exigem bump.

---

## 2. Formato de Commit Obrigatório

```
X.Y.Z - Descrição curta em português
```

**Regras inegociáveis:**

1. A versão **sempre** vem deste `version.md` — bumpe **no mesmo commit** da mudança.
2. Mensagem em **português**, descritiva o suficiente para `git log --grep`.
3. **Proibido** Conventional Commits (`feat:`, `fix:`, `chore:`…) e mensagens vagas.
4. Um objetivo por commit; mudanças pequenas e atômicas.

O bump entra em **um único commit** por entrega (o primeiro). Commits adicionais da
mesma entrega repetem a versão sem novo bump.

---

## 3. Changelog

> Ordem decrescente (mais recente no topo).

### `0.1.1` — 2026-09-02 — Releases automaticas: o version.md da master vira tag e Release

O GitHub nao deduz versao de mensagem de commit: sem tag, o numero e string no
`git log` e `git diff` entre versoes nao existe. Entram o
`.github/workflows/release.yml` e o `tools/release.sh`.

**A regra:** o `version.md` da branch padrao **no GitHub** e o que as Releases
**no GitHub** refletem. Checkout local nao entra na conta. Um PR nao publica
nada; no merge, o push do `version.md` dispara o workflow e a Release vira
aquela versao.

Tag e titulo = a versao pura, sem prefixo `v`. Norma:
[samirhvbr/repodocs](https://github.com/samirhvbr/repodocs/blob/master/docs/versioning.md).

### `0.1.0` — 2026-08-25 — Baseline: a skill, o contrato de saída e a documentação da casa

Nasce o repositório. A skill `resume` é o **merge** de duas skills de terceiros que
o Samir usava em paralelo — [caveman](https://github.com/JuliusBrussee/caveman)
(economia de token, 6 níveis de intensidade) e
[i-have-adhd](https://github.com/ayghri/i-have-adhd) (resposta acionável, etapas
numeradas, teto de 5 itens) — com as divergências resolvidas a favor da
**estrutura**, não da compressão.

**O produto** — `skills/resume/SKILL.md`

- **Esqueleto fixo**: linha de status (`CONCLUÍDO` / `EM ANDAMENTO — etapa X de Y` /
  `BLOQUEADO`) na **linha 1**, comentários numerados, `O QUE PRECISA SER FEITO:` e
  `PRÓXIMA AÇÃO:` — nessa ordem, em toda resposta.
- **`PRÓXIMA AÇÃO:` some quando o status é `CONCLUÍDO`** (ADR-002). Os demais
  cabeçalhos nunca somem: seção vazia colapsa em uma linha.
- **Marcadores seguem o idioma do leitor** (ADR-004), com os conjuntos canônicos
  pt-BR e EN na própria skill e regra de tradução literal e estável para os demais.
- **Sem teto**: listas não são truncadas e profundidade de aninhamento não tem
  limite (ADR-005) — o oposto do teto de 5 itens do i-have-adhd.
- **Assuntos numerados com número estável na sessão** (ADR-006): assunto 3 no turno
  4 continua sendo o assunto 3 no turno 20.
- **Código, comandos e erro são reproduzidos verbatim em todos os níveis**
  (ADR-007); elisão só existe rotulada e contada.
- **Compressão é opcional e nasce desligada** (ADR-001), com **quatro** níveis —
  `lite`, `normal` (default ao ligar), `full`, `ultra`. Ligar sem nomear o nível cai
  em `normal`. Os seis níveis do caveman viraram quatro: os três `wenyan-*` são um
  produto diferente, não um nível.
- **O esqueleto nunca é comprimido** — nem em `ultra`. A compressão só age na prosa
  **dentro** das seções.
- Invariantes de compressão herdados do caveman e mantidos: nunca **acrescentar**
  palavra para soar comprimido, nunca inventar abreviação, nada de setas (`→`),
  nunca derrubar `não`/`nunca`/`só`/`exceto`, números e unidades exatos.

**Documentação** — padrão da casa aplicado

- `README.md` (vitrine em inglês) + `README_br.md` (pt-BR), `CLAUDE.md` + `AGENTS.md`
  espelhados, `SPEC.md` (contrato normativo), `SECURITY.md` (T-01 a T-06),
  `INSTALL.md`, `docs/README.md` (índice), `docs/decisoes.md` (ADR-001 a ADR-008),
  `docs/comparativo.md` (a tabela caveman × i-have-adhd × RESUME que originou o
  projeto), `docs/exemplos.md` (antes/depois nos quatro níveis), `.continue/`
  (escopo F0–F4 + estado), `.claude/` (perfil `opus[1m]` + permissões),
  `.claude-plugin/` (plugin + marketplace), `.gitignore`, `LICENSE` (MIT).

**Limite declarado**: a skill é um **contrato de prompt**, sem hook, sem script e
sem eval automatizado — o cumprimento depende do modelo. As três skills que a
originaram têm o mesmo limite; a diferença é que aqui ele está escrito
(`SECURITY.md` T-01) e virou a fase F2.

_Gatilhos:_ baseline de documentação/versionamento + contrato de saída + configuração
do agente.
