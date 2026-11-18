---
name: deslop
description: Strip agent slop out of a diff before committing. Use before any commit, PR, or code review — removes narrating comments, unsupported guards, dead compatibility paths, unrelated edits, and over-defensive code.
---

# Deslop

Clean the diff, not the codebase. Review every hunk you are about to commit and remove:

- **Narrating comments** — comments that restate what the code does (`// now save the file`), explain your edit history (`// changed from X`), or apologize. Keep only comments explaining *why*, never *what*.
- **Unsupported guards** — try/catch, null checks, and fallbacks for cases that cannot happen given the code's own invariants. If a guard can fire, it needs a test or a comment naming the trigger.
- **Dead compatibility paths** — migration shims, "legacy" branches, and feature flags for versions nobody runs anymore. If a caller still needs one, name the caller.
- **Unrelated edits** — drive-by renames, reformatting, and refactorings outside the change's stated goal. Move them to their own commit or drop them.
- **Over-defensive code** — defensive copies nobody mutates, double validation, belt-and-suspenders checks on data you just validated.

Procedure:

1. `git diff` (staged plus working tree) against the merge base.
2. For each hunk, ask: does this line earn its place toward the stated goal? Flag it if not.
3. Remove or rewrite flagged lines. Prefer deletion over rewrites.
4. Re-run the repo's checks for the touched paths.
5. Report what you removed and why — briefly, one line each.

Slop in prose (PR descriptions, comments, chat replies) is `/unslop`'s job. Slop a reviewer should judge rather than you silently deleting is `/no-comments`'s job.
