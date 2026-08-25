# Exemplos — o esqueleto na prática

> **Normativo por exemplo.** Quando esta página e o [`SPEC.md`](../SPEC.md)
> divergirem, a SPEC vence e esta página está com defeito. Exemplo novo entra aqui
> com bump de `Z` ([`version.md`](../version.md)).
>
> Todos os exemplos abaixo estão em pt-BR porque a conversa era em pt-BR. Em inglês,
> os mesmos exemplos usam `DONE` / `IN PROGRESS` / `BLOCKED` /
> `WHAT NEEDS TO BE DONE:` / `NEXT ACTION:` (SPEC §2).

---

## 1. `EM ANDAMENTO` — um assunto

Compressão desligada (default).

```
EM ANDAMENTO — etapa 2 de 4: corrigir a verificação de token

1. `verifyToken` rejeita o token antes de checar a expiração — `src/auth.ts:42`.
   A ordem das duas condições está invertida: o `return null` do caminho de
   assinatura inválida roda antes do `if (exp < now)`.
2. O middleware engole a exceção, então o 401 chega ao cliente sem corpo — é por
   isso que o front mostra "erro desconhecido" em vez da mensagem real.
3. Os dois pontos de chamada passam o header cru em vez do valor já parseado.
   Funciona hoje por acidente: `jsonwebtoken` tolera o prefixo `Bearer `.

O QUE PRECISA SER FEITO:
1. [x] Inverter as condições em `src/auth.ts:42-58`
2. [ ] Propagar a mensagem da exceção no middleware (`src/middleware/auth.ts:19`)
3. [ ] Trocar o header cru pelo valor parseado nos dois pontos de chamada
4. [ ] Rodar `npm test -- auth.spec.ts`

PRÓXIMA AÇÃO:
Propagar a mensagem no middleware (item 2) antes de mexer nos pontos de chamada —
sem ela, se o item 3 quebrar alguma coisa, o 401 continua sem corpo e você depura
às cegas.
```

**O que verificar neste exemplo:** status na linha 1 · comentários numerados ·
`src/auth.ts:42` exato · lista com estado por item · uma próxima ação, com o porquê.

---

## 2. `EM ANDAMENTO` — vários assuntos (numeração de assunto)

Quando a resposta cobre mais de um assunto, o assunto leva o número de topo e os
comentários aninham. **O número do assunto não muda pelo resto da sessão** (ADR-006).

```
EM ANDAMENTO — etapa 5 de 9: migração da tabela de usuários

1. Auth
   1.1 O item 2 do turno anterior entrou: o middleware agora propaga a mensagem.
   1.2 Sobrou o header cru nos dois pontos de chamada.

2. Migração
   2.1 `users.last_seen` é criada mas nunca preenchida — a migração 0007 adiciona
       a coluna e a 0008 já cria o índice, sem backfill entre as duas.
   2.2 O rollback dropa a coluna sem checar o índice. Em Postgres isso funciona
       (o índice cai junto); no SQLite do ambiente de teste, não.
   2.3 Ninguém lê `last_seen` ainda, então a janela para consertar está aberta.

3. CI
   3.1 O job de teste roda com `--maxWorkers=1` desde março e ninguém sabe por quê.
       Não bloqueia nada agora; anotado como item 6.

O QUE PRECISA SER FEITO:
1. [x] Inverter as condições em `src/auth.ts:42-58`
2. [x] Propagar a mensagem da exceção no middleware
3. [ ] Trocar o header cru pelo valor parseado nos dois pontos de chamada
4. [ ] Escrever o backfill de `users.last_seen` entre 0007 e 0008
5. [ ] Fazer o rollback checar o índice antes do drop
6. [ ] Descobrir por que o CI está preso em `--maxWorkers=1`

PRÓXIMA AÇÃO:
Escrever o backfill (item 4). É o único item cuja janela fecha: assim que algum
código passar a ler `last_seen`, a coluna vazia vira bug em produção em vez de
pendência de migração.
```

**O que verificar:** assunto 3 nasceu aqui e **continuará sendo 3** · o item 6 saiu
do assunto 3 sem virar tangente no meio dos comentários (regra 10) · a lista tem 6
itens porque há 6 itens (ADR-005).

---

## 3. `CONCLUÍDO` — sem `PRÓXIMA AÇÃO:`

```
CONCLUÍDO — login com magic link funcionando em dev

1. As três correções entraram e `npm test -- auth.spec.ts` passa: 14 testes, 0
   falhas.
2. O 401 agora chega com corpo. Reproduzido à mão: token expirado devolve
   `{"error":"token expired"}` em vez de resposta vazia.
3. O `--maxWorkers=1` do CI continua sem explicação, mas não bloqueia nada e
   está registrado como item 6 do assunto 3.

O QUE PRECISA SER FEITO:
1. [x] Inverter as condições em `src/auth.ts:42-58`
2. [x] Propagar a mensagem da exceção no middleware
3. [x] Trocar o header cru pelo valor parseado nos dois pontos de chamada
4. [x] Rodar `npm test -- auth.spec.ts` — 14 passando
```

**O que verificar:** `PRÓXIMA AÇÃO:` **não existe** (ADR-002) · a lista permanece,
toda em `[x]`, e é o registro do que foi entregue · o `CONCLUÍDO` é verdadeiro
porque o teste rodou e passou — se ele não tivesse rodado, o status seria
`EM ANDAMENTO` (T-01).

---

## 4. `BLOQUEADO`

```
BLOQUEADO — falta a credencial do Postgres de staging

1. O backfill está escrito e testado contra o SQLite local: 2.114 linhas
   atualizadas, 0 erro.
2. Rodar contra staging exige `DATABASE_URL` de lá, que não está no ambiente e
   que eu não devo montar por conta própria.
3. O resto da fila não depende disso e pode andar em paralelo.

O QUE PRECISA SER FEITO:
1. [x] Escrever o backfill de `users.last_seen`
2. [!] Rodar o backfill em staging — bloqueado na credencial
3. [ ] Fazer o rollback checar o índice antes do drop
4. [ ] Descobrir por que o CI está preso em `--maxWorkers=1`

PRÓXIMA AÇÃO:
Enquanto a credencial não vem, o item 3 (rollback) anda sozinho e é o de maior
risco dos que sobraram. Começo por ele, salvo se você preferir que eu pare aqui.
```

**O que verificar:** `BLOQUEADO` nomeia **a única coisa que destrava** · o item
bloqueado é `[!]`, não `[ ]` · a próxima ação existe porque ainda há o que fazer.

---

## 5. Preservação literal do erro

Errado — a linha "decisiva" escolhida antes de saber a causa:

```
2. Falha no build: "Cannot find module './config'".
```

Certo:

````
2. O build falha na resolução de `./config`. Saída exata:

   ```
   Error: Cannot find module './config'
   Require stack:
   - /app/dist/server.js
   - /app/dist/index.js
       at Module._resolveFilename (node:internal/modules/cjs/loader:1145:15)
       at Module._load (node:internal/modules/cjs/loader:986:27)
       at Module.require (node:internal/modules/cjs/loader:1233:19)
   ```

   A causa está no `Require stack`, não na primeira linha: quem pede é o
   `dist/server.js` **compilado**, então o arquivo existe em `src/` e não foi
   copiado para `dist/` — é configuração de build, não import errado.
````

O `Require stack` é exatamente a parte que a primeira versão jogou fora, e é ela que
distingue "import errado" de "arquivo não copiado" (ADR-007, T-02).

Saída realmente enorme pode ser elidida — **rotulada e contada**:

```
   [… 240 linhas omitidas — trace completo em build.log …]
```

---

## 6. Os quatro níveis, na mesma resposta

A pergunta: *"por que este componente re-renderiza toda vez?"*

O **esqueleto é idêntico nos quatro** — só a prosa dentro dos comentários muda.

### `off` (default)

```
EM ANDAMENTO — etapa 2 de 4: corrigir o re-render

1. O componente re-renderiza a cada render do pai porque a prop `style` é um
   objeto literal, que cria uma referência nova toda vez. O React compara props
   por referência, então ele sempre enxerga mudança.

O QUE PRECISA SER FEITO:
1. [ ] Envolver o objeto em `useMemo` — `Card.tsx:31`

PRÓXIMA AÇÃO:
Envolver em `useMemo` e rodar o profiler de novo para confirmar que a contagem de
render cai.
```

### `lite`

```
1. A prop `style` é um objeto literal, então ganha referência nova a cada render.
   O React compara por referência e re-renderiza.
```

### `normal`

```
1. Objeto literal na prop `style` = referência nova a cada render = re-render.
```

### `full`

```
1. Objeto inline em prop, referência nova a cada render, re-render.
   Envolver em `useMemo`.
```

### `ultra`

```
1. Objeto inline em prop, referência nova, re-render. `useMemo`.
```

Em todos os cinco, `Card.tsx:31`, `useMemo` e `style` saem exatos, e a linha de
status, a numeração e os dois cabeçalhos continuam lá (SPEC §4.4, invariante 7).

---

## 7. Anti-exemplos

| Errado | Por quê | Certo |
|---|---|---|
| `Ótima pergunta! Vamos ver...` antes do status | Preâmbulo antes da linha 1 | Status na linha 1, sempre |
| `CONCLUÍDO` com um item `[ ]` na lista | Contradição visível; `CONCLUÍDO` é atestado (T-01) | `EM ANDAMENTO — etapa X de Y` |
| `CONCLUÍDO` + `PRÓXIMA AÇÃO: talvez você queira também…` | Seção obrigatória sem matéria-prima vira trabalho inventado (ADR-002) | Omitir a seção |
| Lista com 5 itens e "e outros" | Item somido é trabalho esquecido (T-06) | Os 12 itens, ordenados, dizendo o que a ordem significa |
| `1. Config → novo ref → re-render` | Seta é token próprio e não economiza (invariante 3) | `1. Config, referência nova, re-render` |
| `2. Erro de auth no req` | Abreviação inventada, zero token economizado (invariante 2) | `2. Erro de autenticação na requisição` |
| Renumerar "Migração" de 2 para 1 no turno seguinte | Destrói a única função do número (ADR-006) | Migração continua sendo 2 |
| Mensagem de commit `EM ANDAMENTO — etapa 3 de 7: ...` | Esqueleto vazando para fora do chat (T-07) | `0.2.0 - Corrige a ordem das condições em verifyToken` |
