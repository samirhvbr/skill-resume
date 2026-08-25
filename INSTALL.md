# How to install

The whole skill is **one file**: [`skills/resume/SKILL.md`](skills/resume/SKILL.md).
No dependency, no script, no hook, nothing to build. Every method below is a
different way of putting that file where your harness looks for skills.

> There is exactly **one** copy of `SKILL.md` in this repository, on purpose — no
> mirror under `.claude/skills/`, no symlink. A mirrored copy drifts silently, and a
> symlink does not survive a clone on Windows with git's default settings. Install
> copies; development edits the source.

---

<details open>
<summary><strong>Claude Code — plugin (recommended)</strong></summary>

### Install

```bash
claude plugin marketplace add samirhvbr/skill-RESUME
```

```bash
claude plugin install resume@skill-RESUME
```

Then type `/resume` in any session.

### Verify

```bash
claude plugin list
```

### Update

```bash
claude plugin marketplace update skill-RESUME
```

### Uninstall

```bash
claude plugin uninstall resume@skill-RESUME
```

Or keep it installed and simply do not type `/resume` — the skill has
`disable-model-invocation: true`, so it never turns itself on.

</details>

<details>
<summary><strong>Claude Code — manual, for every project (personal skill)</strong></summary>

### Install

```bash
git clone https://github.com/samirhvbr/skill-RESUME.git /tmp/skill-RESUME
```

```bash
cp -r /tmp/skill-RESUME/skills/resume ~/.claude/skills/resume
```

Then type `/resume`.

### Verify

```bash
cat ~/.claude/skills/resume/SKILL.md | head -5
```

### Update

Re-run the two commands above — `cp -r` overwrites.

### Uninstall

```bash
rm -rf ~/.claude/skills/resume
```

</details>

<details>
<summary><strong>Claude Code — manual, one project only</strong></summary>

From the root of the project that should have it:

```bash
mkdir -p .claude/skills && cp -r /path/to/skill-RESUME/skills/resume .claude/skills/resume
```

Commit `.claude/skills/resume/SKILL.md` and everyone on the project gets `/resume`.

</details>

<details>
<summary><strong>Any other harness</strong></summary>

The skill is a plain Markdown file with YAML frontmatter. Two options:

**1. If the harness loads skills from a directory** — copy `skills/resume/` into it,
keeping the folder name `resume` and the file name `SKILL.md`. That is the whole
install.

**2. If it does not** — paste the body of `skills/resume/SKILL.md` (everything below
the closing `---` of the frontmatter) into whatever file holds your standing
instructions: `AGENTS.md`, `GEMINI.md`, a system prompt, a custom mode. It was
written to work as a standing instruction block, not only as a skill.

In case 2 there is no `/resume` command, so pick the starting state by editing one
line of the pasted text:

- **shape only** (the default): leave it as it is;
- **shape + compression**: append `Compression is ON at level normal.` right under
  the `# resume` heading.

</details>

---

## First run

```text
/resume
```

The confirmation is one line. From then on, every answer has the status line, the
numbered comments, `WHAT NEEDS TO BE DONE:` and `NEXT ACTION:` — in the language you
write in.

**Filler removal is off.** If you also want shorter prose:

```text
/resume compress on
```

That lands on level `normal`. `lite`, `full` and `ultra` are the other three — see
[README.md](README.md#filler-removal-is-optional--and-off-by-default).

To stop: `/resume off` or "stop resume".

⚠️ `normal` is a **compression level**, not the off switch, even though "normal mode"
turns off the two skills this one descends from. It is a deliberate collision
([ADR-003](docs/decisoes.md)) — off is `/resume off`.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `/resume` is not recognised | The file is not where the harness looks. | `ls ~/.claude/skills/resume/SKILL.md` — if it is missing, redo the install. The folder must be `resume` and the file must be `SKILL.md`. |
| The shape came out once, then stopped | Long session drift. | Type `/resume` again. The skill states it never expires, but nothing enforces that — see the declared limitation in [SECURITY.md](SECURITY.md). |
| Answers got shorter and you did not ask | Compression is on from an earlier turn. | `/resume compress off`. The shape stays. |
| The status line shows up in a commit message | The boundary rule was not applied. | Boundaries are in [SPEC.md](SPEC.md) §5.2 — anything persisted outside the chat is ordinary prose. Report it as an issue with the exact text. |
