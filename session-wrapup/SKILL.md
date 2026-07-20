---
name: session-wrapup
description: End-of-session ritual — confidence check on what was built, fix & capture pass for uncaptured lessons, OpenSpec workflow state with the next command, and an optional save-point doc for long exploratory sessions.
disable-model-invocation: true
allowed-tools: "Read, Write, Glob, Grep, Bash(git log:*), Bash(git status:*), Bash(git diff:*), Bash(ls:*), Bash(date:*), Bash(mkdir:*)"
---

# Session Wrap-up

Walk the four steps below over **this session's conversation**, in order, under these exact headings. Be honest and specific — this is a debrief, not a victory lap.

## 1. Confidence check

State your confidence (1–10) in what was built this session. For anything below 9:

- name **exactly what is shaky** — the specific file, assumption, or untested path, not a vague "could be more robust";
- say **what would make it a 10** — the concrete verification or change (a test to run, an edge case to check, a decision to confirm).

If multiple distinct things were built, score them separately.

## 2. Lessons learned

Run the fix & capture loop over the session: look for mistakes that got corrected, user pushback, approaches that were reversed, or friction that repeated.

- For each, propose the capture: a rule (CLAUDE.md line), a skill edit (name the skill and the exact change), or a new memory.
- **Propose, don't apply** — the user decides what to keep.
- If the session was clean, say exactly: `Nothing to capture.` Don't invent lessons to fill the section.

## 3. Workflow state

State which OpenSpec stage the work is in (proposal / spec / implementation / archive — or "not using OpenSpec this session"), and give the **exact command to run first next session**, copy-pasteable. If work stopped mid-task, one line on where.

## 4. Save point

**Only if this was a long exploratory or debugging session** — where the value is in what was learned rather than what was shipped. Skip it (say "Skipped — not an exploratory session") when the work is already captured in commits, specs, or skill edits.

When it applies, write `docs/context/<topic>-<yyyy-mm>.md` (get the date with `date +%Y-%m`; create the directory if needed) for an **LLM reader** picking this up cold:

- what we figured out (conclusions, with the evidence that supports them),
- what we ruled out (so it isn't re-investigated),
- where we stopped and the open questions.

Facts and dead ends, no narrative of the session. Link files as `path:line` where relevant.

## Rules

- Always all four headings, in order — steps that don't apply get their one-line skip note, not silence.
- Ground every claim in the actual session: quote the correction, name the file, cite the commit. No generic retrospective filler.
- The only file this skill may create is the step-4 save point. Everything else is a report.
