---
name: session-handoff
description: Use this skill to capture session state for handoff at natural stopping points. Trigger when a planning session concludes, a long delivery or implementation session wraps up, the context window feels long (roughly 150k+ tokens), the user invokes /handoff, or the user signals they're pausing or stopping. Produces (1) a freeform commit message proposal covering uncommitted changes and (2) a handoff file at plan/{branch-name}/{timestamp}.md capturing agreed delivery phases, next steps, file pointers, open questions, and a ready-to-paste resumption prompt. Does NOT run git commit — only proposes the message. Do NOT use for routine mid-session commits, trivial single-file edits, or when the user has said they'll write the commit themselves.
---

# Session Handoff

Captures the end of a working session so a future Claude session can resume cleanly.

## When to trigger

- User invokes `/handoff` (primary, explicit trigger).
- Session has run long and a natural stopping point arrives.
- Planning session concludes with concrete next steps that won't be executed this session.
- User signals they're pausing, stopping, or wrapping up.

## What to produce

Two outputs, in this order: handoff file first, then commit message proposal.

### 1. Handoff file

**Location:** `plan/{branch-name}/{YYYY-MM-DD-HHmm}.md`

- Resolve `{branch-name}` from `git rev-parse --abbrev-ref HEAD`.
- Generate timestamp from `date +%Y-%m-%d-%H%M`.
- Create the directory if it doesn't exist.

**Template:**

```markdown
# Handoff — {branch} — {timestamp}

## Delivered this session
- Bullet list of completed work, grouped by area if helpful.

## Uncommitted changes at handoff
Output of `git status --short`. Note staged vs unstaged if it matters.

## Commit hash
_Filled in automatically once the handoff + work are committed. Find via:_
`git log -- plan/{branch}/{timestamp}.md`

## Agreed delivery phases (not yet started)
- **Phase name** — one-line description of scope.

## Next step
The first concrete thing the next session should do. Be specific — file, function, decision, whatever.

## Relevant files
- `path/to/file.ext` — why it matters / what state it's in.

## Open questions
- Question — context and what's blocking an answer.

## Resumption prompt

Paste into a fresh Claude session:

> I'm resuming work on `{branch}`. Read `plan/{branch}/{timestamp}.md` first for full context — it has the delivery phases, open questions, and file pointers. The commit containing the handoff is findable via `git log -- plan/{branch}/{timestamp}.md`.
>
> **Next step:** {one-line next step from above}
>
> **Key files to look at:**
> - `{file}` — {why}
>
> **Open questions to resolve before/during this work:**
> - {question}
>
> Confirm you've read the handoff, then proceed with the next step.
```

### 2. Commit message proposal

After writing the handoff file, print a proposed commit message in chat — fenced code block, easy to copy:

```
{subject line — freeform, ~50-72 chars}

{description — paragraph or bullets covering what was delivered,
why, and noting that plan/{branch}/{timestamp}.md is included
for handoff context}
```

**Do not run `git commit`.** Stop after printing the message.

## Workflow

1. `git rev-parse --abbrev-ref HEAD` → branch name.
2. `git status --short` and `git diff --stat` → see what's uncommitted.
3. Generate timestamp.
4. Draft handoff content from session history — what was actually delivered, what's queued, what's unresolved.
5. Write to `plan/{branch}/{timestamp}.md` (create dirs as needed).
6. Print proposed commit message in chat.
7. Stop. User runs `git add . && git commit` themselves.

## Notes

- The handoff file ships with a placeholder for the commit hash. Once the user commits (with the handoff file included), the hash is discoverable via `git log -- plan/{branch}/{timestamp}.md` — no manual edit needed.
- Skip sections that have nothing real in them. An empty "Open questions" header is noise.
- The resumption prompt should be concrete enough that the next session can start work after one read, not three rounds of clarification.
- Match the repo's existing commit style if there's a clear convention in recent `git log` output, even though the format is freeform.
