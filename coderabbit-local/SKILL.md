---
name: coderabbit-local
description: Run CodeRabbit locally with the `cr` CLI against the changes this branch made since it diverged from its base — no PR needed — then triage the findings the same way the `coderabbit` skill triages PR comments. Use when the user wants a CodeRabbit review before pushing or opening a PR, asks to "run coderabbit", "cr review", or wants the bot's take on the current branch.
argument-hint: "[base branch] [--all]"
allowed-tools: "Bash(cr doctor:*), Bash(cr review:*), Bash(git diff:*), Bash(git log:*), Bash(git rev-parse:*), Bash(git status:*), Read, Edit, Grep, Glob"
---

# CodeRabbit (local CLI)

For findings on an existing PR, use the `coderabbit` skill instead — this one reviews the working branch before there is a PR.

## Arguments

Raw arguments: $ARGUMENTS

- A branch name → the **base branch** to review against. If empty, detect the default branch with `git rev-parse --abbrev-ref origin/HEAD` (strip the `origin/` prefix); if that fails, fall back to `main`. Call the result `<base>`.
- `--all` → give 🔵 Trivial the same reading 🟡 Minor gets.

## Instructions

### 1. Preflight with `cr doctor`

```bash
cr doctor
```

It checks the CLI version, storage, auth state, git repository and backend/WebSocket connectivity, and **exits 1 on any failure**. A review takes minutes; this takes seconds — always run it first and stop on a non-zero exit rather than discovering stale auth at the end.

On failure, report which check failed and what fixes it — `cr auth login` for auth, a network/VPN issue for connectivity, [the install docs](https://docs.coderabbit.ai/cli/overview) if `cr` is missing. **Do not install it yourself**, and never pipe an install script into a shell.

Then confirm there is something to review: `git diff --stat <base>...HEAD`. If the branch is empty relative to `<base>`, say so and stop.

### 2. Review

```bash
cr review --base <base> --plain
```

This reviews the branch's tracked changes (committed, staged and unstaged) against `<base>`. It commonly takes several minutes — give the call a generous timeout and don't rerun it because it seems slow. Add `--include-untracked` when new files aren't staged yet, and `--light` only if the user asks for a faster, shallower pass.

If the plain output is hard to attribute to files and severities, rerun with `--agent` for structured JSON rather than guessing.

### 3. Sort by severity

Findings carry the same severity ladder as PR comments: `🔵 Trivial` < `🟡 Minor` < `🟠 Major` < `🔴 Critical`. If a finding has no explicit severity, infer it from the heading (`⚠️ Potential issue` → Major, `🛠️ Refactor suggestion` / `🧹 Nitpick` → Minor) and mark it `(inferred)`.

Severity budgets how much scrutiny a finding earns — it never decides whether the finding is right. A correct, cheap Minor gets fixed; a wrong Major gets rejected.

- 🔵 Trivial → **skipped** by default, one line each with the reason. No verification, no reading files.
- 🟡 Minor → **read it, then decide.** Skip when it is taste, when the project has a settled convention the bot does not know, or when the fix costs more than the defect is worth. Fix when it is correct and the change is small and self-contained. `below severity threshold` is not a reason on its own.
- 🟠 Major, 🔴 Critical → **verify** each one against the current code: read the file at the referenced path and line as it is now, then decide **fix**, **already fixed**, or **reject** (say concretely what the bot missed).
- `--all` → give 🔵 Trivial the same reading 🟡 Minor gets.

Ponytail applies to the fix itself: take the smallest correct change, not necessarily the bot's suggested diff.

### 4. Show the plan, then stop

```
## CodeRabbit — feat/voting vs main (5 findings)

### Fixing (2)

**src/cli.ts:34** — 🟡 Minor · Functional Correctness
`--limit` parsed with `parseInt`, so `--limit 5x` silently becomes 5.
→ `Number()` + isFinite check

**src/rules/loader.ts:41** — 🟠 Major · Functional Correctness
Empty rule file silently yields an empty ruleset instead of erroring.
→ throw on empty parse result

### Not fixing (1)

**src/rules/loader.ts:12** — 🟠 Major · Performance
Claims the regex is recompiled per call; it's module-level const. Bot is wrong.

### Skipped (2)

- src/cli.ts:20 — 🔵 Trivial · Trivial, skipped by default
- README.md:8 — 🟡 Minor · read it: wants the options table alphabetised, the
  order is grouped by workflow on purpose

5 findings, 2 to fix. Apply?
```

Wait for approval. Then apply the approved fixes with `Edit`, and report what changed.

## Rules

- **`cr doctor` first, every time.** A failed check stops the skill; don't attempt the review anyway.
- **The arithmetic must close.** Fixing + not fixing + skipped = findings reported. Print the total in the heading and re-check it before showing the plan.
- **Skipped is a list, not a count.** One line per skipped finding with its path and reason, even for Trivial.
- **Severity budgets attention, not belief.** A Minor is skipped because you read it and judged the change not worth making, never because of its label.
- **No fixes before approval.** Steps 1–3 change nothing on disk.
- **Rejections need a concrete reason** — what the bot missed, not "not applicable".
- **Fix the cause, not the line.** If the same finding pattern hits three files and the bot flagged one, fix all three and say so.
- **Read-only against git.** No commits, no stashing, no branch switching — review what is on disk now.
- **No preamble.** Start with the `## CodeRabbit — <branch> vs <base>` heading.
