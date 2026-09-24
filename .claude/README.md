# Claude Code profile — skill-RESUME

This project's `.claude/` follows the Blue3/samirhvbr pattern: **agent settings +
permissions posture**. There is no code, no dependency and no suite here — the
repository is documentation plus **one** `SKILL.md` —, so the allow-list is the
leanest in the family.

| File | Role |
|---------|-------|
| `settings.json` | **Active** settings (versioned). `effortLevel: xhigh`, `defaultMode: plan`, security deny-list. **No model key** — see below. |
| `README.md` | This file. |

## Rules worth remembering

- **PUBLIC repository.** Everything committed here becomes public and permanent. No
  machine path, session log, client name or excerpt of a private house repository in
  the examples — the examples in [`docs/exemplos.md`](../docs/exemplos.md) are
  synthetic on purpose.
- **Effort `max` goes per session** (`/effort max`); the JSON field accepts up to
  `xhigh`.
- `git filter-branch`/`filter-repo` denied: the `~/x` auto-pusher runs
  `pull --rebase` and undoes a rewrite — rewriting here only breaks the repository.
- `claude plugin` on **ask**: installing or removing a plugin touches Samir's
  environment, not this repository. Testing the installation is his decision, not a
  side effect of a documentation edit.
- `crontab`/`systemctl` on **ask** by house inheritance. This product schedules
  **nothing** and should need neither — if it ever does, that is a new ADR, not a
  permission tweak.

## There is no `.claude/skills/resume/` here

On purpose. The source of truth is
[`skills/resume/SKILL.md`](../skills/resume/SKILL.md) and there is **one** copy in the
repository: a mirrored copy diverges in silence, and a symlink does not survive a
clone on Windows with git's default configuration (`SPEC.md` §6).

To use the skill while developing here (dogfood, recommended), install it in the
personal profile — [`INSTALL.md`](../INSTALL.md) — instead of duplicating the file
inside the repository.

## Product model vs development model

- **Developing this repository: whatever model the session is on.** The model is the
  user's choice, made with `/model`, and a subagent inherits the session's model. This
  repository chooses none (repodocs ADR-027): `settings.json` carries no `model`,
  `fallbackModel` or `availableModels`, and its `env` sets no `ANTHROPIC_MODEL`, no
  `ANTHROPIC_DEFAULT_*_MODEL` and no `CLAUDE_CODE_SUBAGENT_MODEL`. There are no
  stand-by profiles to copy over `settings.json` — `/model` does that. The context
  window comes with the model the user picks; the repository promises none.
- **The product has no model.** The skill is a prompt contract: it runs on whatever
  model the reader is using, and it invokes nothing.
