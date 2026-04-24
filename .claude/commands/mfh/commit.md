# /mfh-commit

You are committing completed work to git using Conventional Commits format.

**Step 1 — Review changes:**
Run `git status` and `git diff` to see all uncommitted changes. Read the output carefully.

**Step 2 — Group into logical commits:**
Analyze the changes and group them into logical commits based on what changed and why. Consider:
- Feature additions vs. bug fixes vs. refactors vs. docs
- Frontend vs. backend vs. config changes
- Related files that belong in the same commit

**Step 3 — Draft commit messages:**
For each proposed commit, write a message using Conventional Commits format:

```
type(scope): description
```

Types: `feat`, `fix`, `chore`, `refactor`, `docs`, `style`, `test`
Scope: the area of the codebase affected (e.g. `draftboard`, `backend`, `rankings`, `keepers`, `admin`, `ui`, `db`, `config`, `mfh`)

Examples:
- `feat(draftboard): add league overview dashboard with draft status cards`
- `fix(keepers): correct manual keeper sync when draft is pending`
- `refactor(rankings): extract tier calculation into tieringHelpers`
- `docs(mfh): initialize MFH planning system`
- `chore(config): update .gitignore for .mfh state files`

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
