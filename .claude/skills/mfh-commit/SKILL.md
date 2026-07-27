---
description: Group changes into logical Conventional Commits
---

# /mfh-commit

You are committing completed work to git using Conventional Commits format.

**Step 1 — Review changes:**
Run `git status` and `git diff` to see all uncommitted changes. Read the output carefully.

**Step 2 — Group into logical commits:**
Analyze the changes and group them into logical commits based on what changed and why. Consider:
- Feature additions vs. bug fixes vs. refactors vs. docs
- Frontend vs. backend vs. config changes
- Related files that belong in the same commit

**Never split a single file's changes across multiple commits** (no `git add -p` / hand-crafted partial-hunk patches) — always stage and commit whole files. If one file's uncommitted diff genuinely spans two unrelated concerns, fold it into whichever commit it fits best rather than slicing it apart; note in the commit plan that the file's other changes are riding along. Hunk-splitting is fragile to get right by hand and the payoff isn't worth it for this repo — only do it if the user explicitly asks for that file to be split.

**Step 3 — Draft commit messages:**
For each proposed commit, write a message using Conventional Commits format:

```
type(scope): description
```

Types: `feat`, `fix`, `chore`, `refactor`, `docs`, `style`, `test`
Scope: the area of the codebase affected. Check `.mfh/library/git.md` first — the project may define specific scope conventions. If not documented, use the affected module, app, or feature name (e.g. `api`, `ui`, `auth`, `db`, `config`).

Examples:
- `feat(auth): add SSO login via OIDC provider`
- `fix(api): correct pagination offset on list endpoint`
- `refactor(ui): extract shared table component`
- `docs(mfh): update milestones and progress`
- `chore(config): add shared package to workspace`

**Step 4 — Present the full commit list:**
Show the user the proposed commits in order, with the files that belong in each one. Ask: "Does this commit plan look good?"

Wait for approval before proceeding.

**Step 5 — Execute commits:**
Once approved, execute each commit in order:
- Stage the relevant files for each commit
- Commit with the message
- Report each commit as it completes

**Step 6 — Confirm and suggest next step:**
Tell the user all commits were made and suggest: "Run `/mfh-push` to push to the remote."
