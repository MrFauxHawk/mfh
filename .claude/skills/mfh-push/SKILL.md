---
description: Push commits to the remote and handle errors
---

# /mfh-push

Run `git push` and report the result.

- If successful: confirm the push and show which branch was pushed to.
- If it fails: show the full error message and suggest how to resolve it. Common issues:
  - No upstream set → suggest `git push -u origin [branch]`
  - Rejected (non-fast-forward) → suggest pulling first: `git pull --rebase origin [branch]`
  - Authentication failure → suggest checking credentials or SSH key setup
