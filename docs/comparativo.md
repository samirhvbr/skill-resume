# Comparativo — caveman × i-have-adhd × RESUME

> Documento de **origem** do projeto. A tabela abaixo é a que o Samir montou ao usar
> as duas skills em paralelo; a terceira coluna é o que o RESUME decidiu em cada
> linha, com o ADR que fechou a decisão.
>
> Origens: [caveman](https://github.com/JuliusBrussee/caveman) (Julius Brussee) ·
> [i-have-adhd](https://github.com/ayghri/i-have-adhd) (Ayoub Ghriss). Nenhum arquivo
> foi copiado de nenhum dos dois — o parentesco é de **regras**, e está creditado
> (ADR-009).

---

## A tabela

| | 🪨 caveman | 🧠 i-have-adhd | 📋 RESUME |
|---|---|---|---|
| **Objetivo** | **Economizar tokens** e deixar respostas compactas | **Organizar a resposta para ser mais fácil de executar** | **Organizar a resposta**; token é consequência (ADR-001) |
| **Filosofia** | "Fale menos" | "Diga primeiro o que preciso fazer" | "Diga onde estamos, o que falta e o que fazer agora" |
| **Remove enrolação** | ✅ Muito forte | ✅ | 🟡 **Opcional, nasce desligado** — 4 níveis (ADR-001, ADR-008) |
| **Reduz tokens** | ✅ **Foco principal** | 🟡 Efeito secundário | 🟡 **Consequência declarada**, sem número alegado (T-05) |
| **Numera etapas** | 🟡 Não é o foco | ✅ | ✅ Etapas **e** comentários |
| **Numera assuntos** | ❌ | ❌ | ✅ Quando o contexto cresce, com **número estável na sessão** (ADR-006) |
| **Mantém estado/progresso** | 🟡 | ✅ | ✅ Linha de status na **linha 1**, todo turno |
| **Marca conclusão** | ❌ | 🟡 "make wins visible" | ✅ `CONCLUÍDO` na **linha 1, antes dos comentários** |
| **Limita listas** | 🟡 | ✅ máximo 5 | ❌ **Sem teto** — truncar é perda de estado (ADR-005, T-06) |
| **Próxima ação clara** | 🟡 | ✅ | ✅ Seção própria, **ausente quando `CONCLUÍDO`** (ADR-002) |
| **Vários níveis** | ✅ 6 níveis | ❌ | ✅ **4 níveis**, atrelados ao opcional de compressão (ADR-008) |
| **Profundidade de aninhamento** | 🟡 | 🟡 | ✅ **Sem teto** (ADR-005) |
| **Preserva código/erros exatamente** | ✅ (mas manda citar "a linha decisiva" do log) | Não é o foco | ✅ **Verbatim em todos os níveis**, elisão só rotulada e contada (ADR-007) |
| **Subagents / ferramentas extras** | ✅ | 🟡 | ❌ Fora de escopo na v1 |
| **Melhor para programação pesada** | ✅ | ✅ | ✅ |
| **Melhor para tarefas complexas/longas** | 🟡 | ✅ | ✅ |

---

## O que o RESUME herdou de cada um

**Do caveman:**

- a escada de níveis de compressão (reduzida de 6 para 4 — ADR-008);
- os invariantes que impedem a compressão de virar mutilação: nunca **acrescentar**
  palavra para soar comprimido, nunca inventar abreviação (`cfg`, `impl`, `fn`),
  nada de setas (`→`), nunca derrubar `não`/`nunca`/`só`/`exceto`, número e unidade
  exatos;
- a **auto-clareza** — aviso de segurança, ação irreversível e ambiguidade técnica
  suspendem a compressão sozinhos;
- a regra de **fronteira**: o estilo vale para o chat, não para commit, issue, doc
  ou mensagem a terceiro;
- a preservação do **idioma do leitor**;
- a persistência explícita ("vale até o leitor desligar").

**Do i-have-adhd:**

- numerar trabalho de múltiplas etapas, uma ação delimitada por item;
- **restabelecer o estado a cada turno** — o leitor não segura "etapa 3 de 5" entre
  mensagens;
- fechar com **uma** próxima ação concreta;
- tom factual para erro (sem "opa", sem "parece que houve um problema");
- sem preâmbulo, sem recapitulação, sem fecho de cortesia;
- o **pre-send check** como último passo antes de enviar;
- as exceções: pedido de "explica", ação destrutiva, espiral de depuração,
  ambiguidade real.

## O que o RESUME recusou dos dois

| Recusado | De quem | Por quê |
|---|---|---|
| Compressão sempre ligada | caveman | Faz o produto virar "caveman com cabeçalho": todo conflito entre cortar e explicar seria resolvido a favor de cortar (ADR-001). |
| Citar só "a linha decisiva" do log | caveman | Escolher a linha decisiva é julgamento feito **antes** de saber a causa; quando erra, o leitor depura outra coisa (ADR-007, T-02). |
| Níveis `wenyan-*` | caveman | Responder em chinês clássico é outro produto, e colide com responder no idioma do leitor (ADR-008, ADR-004). |
| Teto de 5 itens | i-have-adhd | Boa ergonomia de leitura, perda de estado na execução — a lista do RESUME é a memória entre turnos (ADR-005, T-06). |
| Não ter níveis | i-have-adhd | Sem escada, o opcional de compressão vira liga/desliga grosso demais para uso real (ADR-008). |

## A diferença em uma frase

- **caveman**: a mesma resposta, com menos palavras.
- **i-have-adhd**: a mesma resposta, começando pela ação.
- **RESUME**: a mesma resposta, sempre no mesmo formato — status, comentários
  numerados, o que falta, o que fazer agora — e, **se você pedir**, com menos
  palavras.
