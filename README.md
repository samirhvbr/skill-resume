# skill-RESUME

**Every answer in the same shape: where we are, what is left, what to do next.**

A skill for Claude Code (and any harness that loads a `SKILL.md`) that fixes the
*structure* of the answer instead of its length. Line 1 always tells you the status.
The comments are always numbered. The last two sections are always what remains and
what to do now.

Cutting tokens is a **consequence** here, never the goal — filler removal is
optional and **off by default**.

> 🇧🇷 **Versão em português:** [README_br.md](README_br.md) — *all project
> documentation is written in Brazilian Portuguese by convention; this README, the
> install guide and the skill itself are the English-facing surface.*
>
> **The skill:** [skills/resume/SKILL.md](skills/resume/SKILL.md) (the product — this
> is the file the model loads) · [INSTALL.md](INSTALL.md).
>
> **Documentation:** [CLAUDE.md](CLAUDE.md) / [AGENTS.md](AGENTS.md) (rules for
> whoever develops this repo) · [SECURITY.md](SECURITY.md) (threat model —
> **required reading**) · [SPEC.md](SPEC.md) (the normative output contract) ·
> [docs/README.md](docs/README.md) (technical index) ·
> [docs/decisoes.md](docs/decisoes.md) (ADRs) ·
> [docs/comparativo.md](docs/comparativo.md) (where this came from) ·
> [docs/exemplos.md](docs/exemplos.md) (worked examples) ·
> [version.md](version.md) (version + commit format) ·
> [.continue/estado-atual.md](.continue/estado-atual.md) (where the project stands).

## The shape

```
<STATUS LINE>                    ← always line 1

<numbered comments — the substance>

WHAT NEEDS TO BE DONE:
1. [x] ...
2. [ ] ...

NEXT ACTION:
<one recommended next step>
```

| Status | When |
|---|---|
| `DONE` | Everything asked for is finished **and verified**. `NEXT ACTION:` disappears entirely. |
| `IN PROGRESS — step 3 of 7: <title>` | Work is under way and something is left. |
| `BLOCKED — <reason>` | Cannot proceed without a decision, credential or answer. |

`DONE` is a claim about reality, not a way to close a message. Failing tests are not
`DONE`. A skipped step is not `DONE`. When in doubt the status is `IN PROGRESS`.

## What changes

**Before**

> Great question! Let me take a look at this. Your auth flow has a few moving pieces
> — the middleware, the token verification and the cookie handling. Looking at
> `src/auth.ts`, the `verifyToken` function seems to check things in the wrong
> order. One approach would be to swap the conditions around. After that you'd
> probably want to run the auth tests. By the way, you might also want to look at
> your dependency versions overall. Hope this helps! Let me know if you want to dig
> deeper.

**After**

```
IN PROGRESS — step 2 of 4: fix the token check

1. `verifyToken` rejects the token before checking expiry — `src/auth.ts:42`.
   The two conditions are inverted: the invalid-signature `return null` runs
   before `if (exp < now)`.
2. The middleware swallows the exception, so the 401 reaches the client with no
   body — that is why the front end shows "unknown error".

WHAT NEEDS TO BE DONE:
1. [x] Invert the conditions in `src/auth.ts:42-58`
2. [ ] Propagate the exception message in `src/middleware/auth.ts:19`
3. [ ] Run `npm test -- auth.spec.ts`
4. [ ] Separately: dependency versions are stale — not blocking

NEXT ACTION:
Propagate the message in the middleware (item 2) before touching anything else —
without it, if step 3 fails you are debugging a 401 with no body.
```

The tangent did not vanish and it did not interrupt: it became item 4.

## Install

```bash
git clone https://github.com/samirhvbr/skill-RESUME.git
cp -r skill-RESUME/skills/resume ~/.claude/skills/resume
```

Then, in any session:

```text
/resume
```

Other harnesses, plugin install and per-project install: [INSTALL.md](INSTALL.md).

## Commands

| Command | Effect |
|---|---|
| `/resume` | On, **compression off**. The shape only. |
| `/resume compress on` | Compression on at level `normal`. |
| `/resume compress off` | Compression off. The shape stays. |
| `/resume lite` \| `normal` \| `full` \| `ultra` | On, with compression at that level. |
| `/resume off` | Off. Also: "stop resume". |

> ⚠️ `normal` is a **compression level**, not the off switch — even though "normal
> mode" turns off the two skills this one descends from. Off is `/resume off` or
> "stop resume". This collision is deliberate and documented, not an oversight
> ([ADR-003](docs/decisoes.md)).

## Filler removal is optional — and off by default

Out of the box this skill changes the *shape* of the answer and nothing else: full
sentences, full explanations, ordinary prose inside the sections. **You have to turn
compression on.** That is the one decision that separates this skill from the two it
merges: the structure is the product, and a smaller token count is a side effect of
it, not a target to chase.

Turn it on and you get four levels, lightest to heaviest:

| Level | What it removes |
|---|---|
| `lite` | Pleasantries, preamble, recap, closing lines, empty hedging. Articles, full sentences and normal grammar stay. |
| `normal` *(default when you enable it)* | `lite` + short synonyms, no tool-call narration, no decorative tables or emoji, each fact stated once. |
| `full` | `normal` + articles dropped, fragments allowed, telegraphic prose. |
| `ultra` | `full` + conjunctions dropped where cause-and-effect stays unambiguous, one word where one word is enough. |

**The shape is never compressed.** At `ultra`, the status line, the numbering and
the section headers are exactly what they are at `off`. Compression works on the
prose *inside* the sections.

Compression also **suspends itself** — without being asked — for security warnings,
irreversible-action confirmations, and any case where dropping words would make the
meaning readable two ways.

## What it never does

- **Never caps a list.** Twelve findings means twelve items. Ranking, yes;
  truncating, no; "and others", never. The pending list is the memory between turns,
  so a dropped item is lost work, not tidier formatting.
- **Never caps nesting depth.** `2.1.3.1` is fine when the material is that deep.
- **Never touches code or error text.** Code, diffs, commands, paths, line numbers,
  stack traces, log lines and identifiers are reproduced verbatim at *every* level.
  Elision exists, but only labelled and counted: `[… 240 lines omitted …]`.
- **Never renumbers a subject.** Subject 3 in turn 4 is still subject 3 in turn 20.
- **Never leaks the shape outside the chat.** Commit messages, issues, PRs, docs and
  messages to third parties are written as ordinary prose, with no status line.

## Where this came from

A merge of two skills, used side by side until the overlap became obvious:

- 🪨 [**caveman**](https://github.com/JuliusBrussee/caveman) by Julius Brussee —
  compression levels, and the invariants that keep compression from becoming
  mutilation (never *add* a word to sound terse, never invent abbreviations, never
  drop a negation).
- 🧠 [**i-have-adhd**](https://github.com/ayghri/i-have-adhd) by Ayoub Ghriss —
  numbered steps, restating state every turn, one concrete next action, no preamble
  and no closing pleasantries.

The full comparison — including the five things RESUME **refused** to inherit and
why — is in [docs/comparativo.md](docs/comparativo.md). No file was copied from
either project; the kinship is in the rules, and it is credited on purpose.

## Status

**Version `0.1.0` — the skill is written and installable.**

Declared limitation: this is a **prompt contract**. Nothing fails when the shape
does not come out — there is no hook, no script, no eval. The two skills it descends
from share that limitation; the difference here is that it is written down
([SECURITY.md](SECURITY.md), [SPEC.md](SPEC.md) §7) and is the next phase of work
rather than an unstated assumption.

No token-saving percentage is claimed anywhere in this repository, because nothing
has been measured yet. That is deliberate: no number without a benchmark that
produces it.

## License

MIT — see [LICENSE](LICENSE).
