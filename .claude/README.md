# Perfil de modelo Claude Code — skill-RESUME

`.claude/` deste projeto segue o padrão dos repos Blue3/samirhvbr: **perfil de
modelo + postura de permissões**. Aqui não há código, dependência nem suíte — o
repositório é documentação mais **um** `SKILL.md` —, então a allow-list é a mais
enxuta da família.

| Arquivo | Papel |
|---------|-------|
| `settings.json` | Perfil **ativo** (versionado). Opus-only `opus[1m]`, `effortLevel: xhigh`, `defaultMode: plan`, deny-list de segurança. |
| `README.md` | Este arquivo. |

## Regras que valem lembrar

- **Repositório PÚBLICO.** Tudo que entra em commit aqui fica público e permanente.
  Nada de caminho de máquina, log de sessão, nome de cliente ou trecho de repo
  privado da casa nos exemplos — os exemplos de [`docs/exemplos.md`](../docs/exemplos.md)
  são sintéticos de propósito.
- **Não adicionar `CLAUDE_CODE_DISABLE_1M_CONTEXT`** — é ela que derruba a janela
  para 200K.
- **Effort `max` vai por sessão** (`/effort max`); o campo do JSON aceita até
  `xhigh`.
- `git filter-branch`/`filter-repo` negados: o auto-pusher de `~/x` faz
  `pull --rebase` e desfaz reescrita — reescrever aqui só quebra o repo.
- `claude plugin` em **ask**: instalar ou remover plugin mexe no ambiente do Samir,
  não neste repositório. Testar a instalação é decisão dele, não efeito colateral de
  uma edição de documentação.
- `crontab`/`systemctl` em **ask** por herança da casa. Este produto **não** agenda
  nada e não deveria precisar dos dois — se um dia precisar, isso é ADR novo, não
  ajuste de permissão.

## Não existe `.claude/skills/resume/` aqui

De propósito. A fonte da verdade é
[`skills/resume/SKILL.md`](../skills/resume/SKILL.md) e existe **uma** cópia no
repositório: cópia espelhada diverge em silêncio, e symlink não sobrevive a clone em
Windows com a configuração padrão do git (`SPEC.md` §6).

Para usar a skill enquanto desenvolve aqui (dogfood, recomendado), instale-a no
perfil pessoal — [`INSTALL.md`](../INSTALL.md) — em vez de duplicar o arquivo dentro
do repositório.

## Modelo do produto vs modelo do desenvolvimento

- Desenvolvimento deste repo: Opus (perfil acima).
- **O produto não tem modelo.** A skill é um contrato de prompt: ela roda em qualquer
  modelo que o leitor estiver usando, e não invoca nada. É a diferença em relação ao
  skill-COMMITTER, que tem um fallback Sonnet configurado.
