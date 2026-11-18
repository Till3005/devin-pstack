### Opening a PR

Invoked at the end of every other playbook.

Work from a git worktree off trunk, and keep unrelated work out of it. Rebase into small, landable commits. Run `/deslop` from `this-plugin` over the diff before commit.

Title: `type(scope): subject`, naming a real symbol when one carries the change. Body: a one-minute briefing under 40 lines with these `##` headings in order, dropping empty ones: `## Why`, `## What changed`, `## Scope`, `## Tradeoffs` (only rejected alternatives a reviewer would otherwise ask about), `## Blast Radius`, `## Verification`.

Stack follow-ups by base branch. Open PRs ready: `git_create_pr` with `draft: false` (after `fetch_pr_template`), or `gh pr create` without `--draft`. On GitHub, register a true patch series with `git_stack` `create` once each PR's base is the head of the PR below it. Opening a PR does not start a babysit.
