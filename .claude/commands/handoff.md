---
description: Wrap up the session — write a handoff file and propose a commit message
argument-hint: [optional focus or context note]
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git rev-parse:*), Bash(date:*), Bash(mkdir:*), Write
---

Run the session-handoff skill workflow to capture this session for resumption.

## Context (auto-collected)

- Branch: !`git rev-parse --abbrev-ref HEAD`
- Timestamp: !`date +%Y-%m-%d-%H%M`
- Uncommitted changes: !`git status --short`
- Diff stats: !`git diff --stat`
- Recent commits (for tone/style of commit message): !`git log --oneline -5`

## Optional user note

$ARGUMENTS

## Your task

1. Write a handoff file at `plan/{branch}/{timestamp}.md` following the **session-handoff** skill template — delivered work, uncommitted changes, commit hash placeholder, agreed delivery phases, next step, relevant files, open questions, and a resumption prompt. Create the directory if it doesn't exist.
2. Print a proposed commit message in chat (subject ~50–72 chars + description) covering both the session's work and the new handoff file. Use a fenced code block for easy copy.
3. Stop. Do **not** run `git commit` — the user commits themselves.

If there's nothing meaningful to hand off (trivial session, nothing uncommitted, no agreed next steps), say so plainly and skip the file.
