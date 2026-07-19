# skills

Personal [Claude Code skills](https://code.claude.com/docs/en/skills): each top-level directory holds one skill as `<name>/SKILL.md`. They live here centrally and get symlinked into projects (or globally) with `link.sh`.

## Skills

| Skill | What it does |
|---|---|
| `checklist` | Convert the current plan/review/task list in context into a persistent markdown checklist under `.claude/plans/` |
| `code-review` | Review staged changes or a specific area, optionally delegating to a chosen agent |
| `feature-generator` | Expand `spec.md` into a dependency-ordered `features.md`, and keep the two in sync |
| `first-five` | Scan a diff against the First Five checklist (error handling, input boundaries, external calls, state mutations, assumed dependencies) |
| `preflight` | Production pre-flight checklist for a branch: env vars, config, migrations — everything needed once it merges |
| `review-order` | Scannable review checklist grouped by feature, four-pass order (types, data flow, business logic, edge cases) |
| `spec-generator` | Turn a vague product idea (plus sketches/notes) into a structured product spec |
| `triage` | Group a diff into feature areas with risk tiers to decide where review time goes |
| `warm` | Vet dependencies a branch adds against the WARM check (Worth it, Alive, Right-sized, Maintained securely) plus a supply-chain Safety check (install scripts, typosquatting, release freshness) |
| `zombies` | Suggest tests worth writing via the ZOMBIES heuristic (Zero, One, Many, Boundaries, Interface, Exceptions, Simple) |

Most are slash-command only (`disable-model-invocation: true`) and take an optional base branch as argument, defaulting to `main`.

## Linking

`link.sh` symlinks a skill into a project's `.claude/skills/` (relative links, so they survive in git) or into `~/.claude/skills` for global use.

```bash
./link.sh triage /Project/d2ass          # link one skill into a project
./link.sh all /Project/d2ass             # link every skill
./link.sh triage global                  # link into ~/.claude/skills
./link.sh triage /Project/d2ass --unlink # remove the link (also works with `all`)
```

It refuses to overwrite existing files or links pointing elsewhere — use `--unlink` first.

## Adding a skill

Create `<name>/SKILL.md` with `name` and `description` frontmatter; `link.sh all` picks it up automatically.

## Notes

Use `spec-generator` and `feature-generator` skills only if you don't want to follow [OpenSpec framework](https://github.com/fission-ai/openspec). Otherwise, don't link them to your project.