# Documentação técnica — skill-RESUME

Índice de `docs/`. Documentação **durável** mora aqui; notas de trabalho, escopo e
estado em [`.continue/`](../.continue/); contrato normativo em [`SPEC.md`](../SPEC.md);
o produto em [`skills/resume/SKILL.md`](../skills/resume/SKILL.md).

> Projeto em **F1**: a skill existe e está escrita; **não existe verificação
> automatizada de cumprimento**. O que estiver marcado com ⛔ na `SPEC.md` §7 é
> lacuna conhecida, não esquecimento.

## Nesta pasta

| Arquivo | O que é |
|---|---|
| [decisoes.md](decisoes.md) | **ADRs.** ADR-001 a ADR-009 + pendências P-01 a P-05. Decisão nova entra aqui, nunca dentro de um how-to. |
| [comparativo.md](comparativo.md) | **Documento de origem.** A tabela caveman × i-have-adhd × RESUME, o que foi herdado de cada um e o que foi recusado, com o ADR de cada linha. |
| [exemplos.md](exemplos.md) | **Normativo por exemplo.** Os quatro status, numeração de assunto, preservação literal de erro, os quatro níveis lado a lado e os anti-exemplos. |

## Fora desta pasta

| Arquivo | O que é |
|---|---|
| [../skills/resume/SKILL.md](../skills/resume/SKILL.md) | **O produto.** É este arquivo que o modelo carrega — fonte da verdade do comportamento. |
| [../README.md](../README.md) | Vitrine em **inglês**: o que a skill faz, instalação, os quatro níveis. |
| [../README_br.md](../README_br.md) | Mesma vitrine em pt-BR. |
| [../SPEC.md](../SPEC.md) | **Normativo.** Esqueleto, marcadores por idioma, preservação literal, escada de compressão, fronteiras, lacunas. |
| [../SECURITY.md](../SECURITY.md) | Modelo de ameaça (T-01 a T-07). **Leitura obrigatória.** |
| [../INSTALL.md](../INSTALL.md) | Instalação por plugin, por cópia manual e em outros harnesses. |
| [../version.md](../version.md) | Fonte de verdade da versão, gatilhos de bump, formato de commit, changelog. |
| [../CLAUDE.md](../CLAUDE.md) / [../AGENTS.md](../AGENTS.md) | Regras de quem desenvolve este repo. Espelhados — editar os dois. |
| [../.continue/escopo-projeto.md](../.continue/escopo-projeto.md) | Fases F0–F4, com critério de pronto. |
| [../.continue/estado-atual.md](../.continue/estado-atual.md) | Onde o projeto está e o que precisa do Samir. |
| [../.claude/README.md](../.claude/README.md) | Perfil de modelo e postura de permissões. |

## Por onde começar

- **Entender o produto** → [`comparativo.md`](comparativo.md), depois
  [`exemplos.md`](exemplos.md). A tabela explica *por que* o formato é esse; os
  exemplos mostram o formato.
- **Vai mexer na skill** → [`../SPEC.md`](../SPEC.md) + [`../SECURITY.md`](../SECURITY.md)
  **antes** de abrir a `SKILL.md`. Toda regra de lá tem uma ameaça atrás.
- **Vai mexer na compressão** → SPEC §4 inteira, e T-02 e T-03 do `SECURITY.md`.
- **Vai implementar a verificação (F2)** → P-01 em [`decisoes.md`](decisoes.md), e o
  `evals/` do [i-have-adhd](https://github.com/ayghri/i-have-adhd) como referência
  de formato.

## Convenções

- **pt-BR em tudo aqui.** As exceções são de vitrine e estão listadas no
  [`CLAUDE.md`](../CLAUDE.md): `README.md`, `INSTALL.md` e a própria
  `skills/resume/SKILL.md` saem em inglês.
- Documento novo aqui entra **neste índice** no mesmo commit.
- Sem link para arquivo inexistente; futuro se descreve em texto.
- Distinguir **fato observado**, **inferência** e **recomendação**.
- Número alegado precisa de algo que o conte — sem benchmark, sem porcentagem
  (T-05).
