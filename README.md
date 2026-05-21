# Claude Code — Session Handoff

Long [Claude Code](https://claude.com/claude-code) sessions hit a wall: context fills
up, the model forgets what you decided hours ago, and resuming the next day means
re-explaining everything.

`/handoff` is a small skill plus slash command that fixes this. At the end of a
session it does two things:

1. **Writes a handoff file** to `plan/{branch}/{timestamp}.md` — what was delivered,
   what's uncommitted, agreed next phases, the concrete next step, relevant files,
   open questions, and a ready-to-paste resumption prompt for the next session.
2. **Proposes a commit message** covering both the work and the handoff file. You
   commit it yourself — the skill stays out of `git commit`.

Because the handoff lives in the repo, `git log -- plan/...` finds it later, so
future-you (or future-Claude) gets full context from one read. The skill also nudges
Claude to trigger itself when context feels heavy (~150k tokens) or when you signal
you're wrapping up — not just on the explicit slash command.

## Contents

| File | Purpose |
|------|---------|
| `.claude/skills/session-handoff/SKILL.md` | The `session-handoff` skill — when to trigger and what to produce. |
| `.claude/commands/handoff.md` | The `/handoff` slash command that runs the skill workflow. |

## Installation

Copy the `.claude/` directory into a project (or merge it into your existing one):

```bash
cp -r .claude /path/to/your/project/
```

For global availability across all projects, copy the contents into `~/.claude/`
instead:

```bash
cp -r .claude/skills/session-handoff ~/.claude/skills/
cp .claude/commands/handoff.md ~/.claude/commands/
```

## Usage

Inside Claude Code, run:

```
/handoff [optional focus or context note]
```

Claude collects branch, timestamp, and uncommitted changes automatically, then:

1. Writes a handoff file to `plan/{branch}/{YYYY-MM-DD-HHmm}.md`.
2. Prints a proposed commit message for you to copy.
3. Stops — you commit yourself.

The skill also triggers automatically at natural stopping points (long sessions,
concluded planning, when you signal you're wrapping up).

## License

[MIT](LICENSE)
