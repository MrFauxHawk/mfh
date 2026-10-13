---
description: Push commits to the remote, or (in release-branch projects) release main to the release branch, following git.md's Push & Release rules
---

# /mfh-push

You are pushing committed work to the remote. What a push *means* differs per project, so read the project's own rules first. There are two release styles:
- **Single branch:** a push is just a push. Depending on the project it's a harmless backup, or it deploys (CI).
- **Release branch:** production runs from a separate branch (usually `release`). Pushing `main` is only a backup. A *release* fast-forwards the release branch to `main` and pushes it. What ships next (CI, or deploy scripts the user runs) is recorded in git.md.

**Step 1 — Read the project's push rules:**
Read the **Push & Release** section of `.mfh/library/git.md`:
- **Release style:** `single branch` or `release branch`
- **Release branch:** the branch production runs from, e.g. `release` (release-branch style only)
- **What a push does:** whether pushing `main` changes anything live
- **After a release:** what happens once the release branch is pushed (release-branch style only), e.g. "CI deploys it" or "run `deploy.sh`"
- **Release tags:** `date`, `semver`, `none` or `n/a`. This is how each release is tagged (Step 5).
- **Hold:** a temporary "don't push `main` until …", or `none`

**If the section is missing, or still holds `/mfh-init`'s bracketed placeholders:** set it up once now (Step 1a), then continue.

**Step 1a — Set up the section (only when missing):**
Before asking, check `.mfh/library/deploy.md` and the repo: CI config (`.github/workflows/`, `.gitlab-ci.yml`, `.gitea/workflows/`), deploy scripts, and whether a local `release` branch exists. If the answer is evident, put that option first and mark it (Recommended).

Ask via `AskUserQuestion`: "How does code reach production in this project?" — **Pushing main deploys it** (CI/CD) / **I deploy manually** (a script or command; pushing changes nothing live) / **From a release branch** (production runs only from a separate branch that `main` is released into) / **Not deployed yet**.

If they pick release branch, also ask what happens after it's pushed: **CI deploys it automatically** / **I run a deploy command** (ask which, free text).

Write the section into `git.md`, after **Branch Rules** (create that heading if needed):

```markdown
## Push & Release
- **Release style:** [single branch | release branch]
- **Release branch:** [`release` | none]
- **What a push does:** [one sentence, e.g. "Pushing `main` deploys to production automatically (CI)." / "Nothing live; deploys are run manually (…)." / "Nothing live; production runs only from `release`." / "Nothing live; not deployed yet."]
- **After a release:** [e.g. "CI deploys `release` automatically." / "Run `<command>`." | n/a]
- **Release tags:** [date | semver | none | n/a]
- **Hold:** none
```

For **Release tags**, when the answer was "pushing main deploys it" or "from a release branch", ask the same question `/mfh-init` asks, with the same recommendation rule: **Date tags** for internal apps and websites, **Semantic versions** for libraries, packages, APIs and installable apps others depend on. Otherwise write `n/a`.

Tell the user the section was added. It's an uncommitted edit to `git.md` that `/mfh-commit` will pick up.

**Step 2 — Choose the kind of push:**
- **Single branch:** do a normal push (Step 3).
- **Release branch:** ask via `AskUserQuestion`: "What kind of push?" — **Normal push** (back up `main`; nothing ships) / **Release** (ship `main` to `<release branch>`). Normal push → Step 3. Release → Step 4.

**Step 3 — Normal push:**
1. **List what's going up:** `git log --oneline @{u}..HEAD` (with no upstream yet, `git log --oneline HEAD --not --remotes`). If it's empty, say the branch is up to date and stop.
2. **Respect a hold:** if **Hold** isn't `none`, show it and ask via `AskUserQuestion`: "git.md has a push hold: [hold text]. Push anyway?" — **Don't push** (Recommended) / **Push anyway**. Never edit or clear a hold yourself; the user sets and clears it.
3. **Confirm only if this deploys:** if **What a push does** says pushing deploys, ask via `AskUserQuestion`: "This push deploys to production: [N] commit(s) listed above. Push?" — **Push** / **Cancel**. Otherwise the user already asked by running this command, so just push.
4. Run `git push` and report the result (Step 6 covers errors).
5. **Tag it if the push deployed:** if **What a push does** says pushing deploys and **Release tags** is `date` or `semver`, tag the pushed commit (Step 5).

**Step 4 — Release (release-branch style only):**
Let `R` be the release branch and `main` the development branch named in **Branch Rules** (default `main`). Never switch the working checkout's branch at any point. Move `R` only with `git update-ref`.

1. **Hotfix guard:** if `git worktree list --porcelain` shows `R` checked out in a worktree, a hotfix is in progress. Stop and say so: finish it, remove the worktree, and merge `R` into `main` first.
2. **Release branch missing:** if `R` doesn't exist locally, ask via `AskUserQuestion` which commit production runs right now: **The current main** (first release) / **An earlier commit** (ask for it). Create `R` there with `git branch R <commit>`.
3. **Check the remote:** run `git fetch origin R` (ignore "couldn't find remote ref" on a first release). If `origin/R` has commits that local `R` lacks, stop and report it. Something moved the remote release branch outside this flow.
4. **Fast-forward check:** if `R` isn't an ancestor of `main` (`git merge-base --is-ancestor R main`), `R` has a commit `main` lacks. That's almost always a hotfix that was never merged back. List those commits (`git log --oneline main..R`) and offer via `AskUserQuestion`: **Merge `R` into `main` now** (`git merge R` in the working checkout, which is on `main`; stop if it conflicts or uncommitted changes block it) / **Stop**. Never force-move `R`, because that would drop the hotfix from production.
5. **Pick the target:** if `R` already contains `main`, say there's nothing new to release and skip to sub-step 9 (the push may still be needed). Otherwise ask via `AskUserQuestion`: **Release everything on main** / **Only up to a certain commit** (show `git log --oneline R..main` and ask which commit; it must be on `main`).
6. **Show what will ship:**
   - The commit list: `git log --oneline --reverse R..<target>`.
   - Separately, any new database migrations (added files under any `migrations/` directory: `git diff --name-only --diff-filter=A R <target>`).
   - Any deploy or infra changes: Dockerfiles, compose files, CI config, a `deploy/` directory.
   - Also mention that uncommitted changes are never included.
7. **Hold:** if **Hold** isn't `none`, show it. A release ships exactly the work a hold usually protects, so ask whether this is the release the hold was waiting for. Don't clear the hold yourself. Remind the user to remove it from git.md if it's now done.
8. **Confirm:** ask via `AskUserQuestion`: "Release [N] commit(s) to `R`?" — **Release** / **Cancel**. Then run `git update-ref -m "mfh-push release" refs/heads/R <target> <old R sha>`. The old-value check refuses if `R` moved in the meantime.
9. **Tag and push:** if **Release tags** is `date` or `semver`, create the tag on `<target>` (Step 5). Then run `git push origin R` (plus `git push origin <tag>` if you tagged). Then, if `main` has unpushed commits, offer via `AskUserQuestion` to push `main` too as a backup (show the hold if there is one).
10. **Next step:** tell the user what **After a release** says, e.g. "Now run `prod-build.sh`, then `prod-deploy.sh`." Never run build or deploy commands yourself unless the project's docs explicitly allow it.

**Step 5 — Tag the release (when Release tags is `date` or `semver`):**
1. **Find the previous release tag:** `git describe --tags --abbrev=0 --match <pattern> <commit>^`, where the pattern is `[0-9][0-9][0-9][0-9].*` for date tags or `v[0-9]*` for semver. If there isn't one, this is the first tagged release.
2. **Name it:**
   - **date:** today as `YYYY.MM.DD`. If that tag already exists, use `YYYY.MM.DD.2`, `.3` and so on.
   - **semver:** suggest a bump from what's shipping and ask via `AskUserQuestion`. Suggest **patch** for fixes only, **minor** for new features, and **major** for a breaking change to something others depend on. Show the resulting tag on each option.
3. **Write the release notes:** first, the phases whose closing commit (`docs(mfh): close …`) falls in `<previous tag>..<commit>` (or all history for a first release), each with the one-line summary of its entry in `.mfh/state/built.md`. Then a short "Also included" list of any other commit subjects in the range that aren't part of those phases. Keep it plain-language and short; it's a pointer, not a changelog.
4. **Show and confirm:** show the tag name and notes and ask via `AskUserQuestion`: **Tag it** / **Edit the notes** / **Skip tagging**.
5. **Create it:** `git tag -a <tag> <commit> -F <notes file>`, an annotated tag. Push it alongside the branch. Never move, reuse or delete an existing tag.

**Step 6 — Push errors:**
If a push fails, show the full error message and suggest how to resolve it. Common issues:
- No upstream set → suggest `git push -u origin [branch]`
- Rejected (non-fast-forward) → suggest pulling first: `git pull --rebase origin [branch]`. Never suggest this for the release branch; report that instead.
- Authentication failure → suggest checking credentials or SSH key setup
