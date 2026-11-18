---
name: setup-pstack
description: Configure which Devin agent modes pstack uses per role. Detects the modes available to you and writes an always-applied rule that overrides the skill defaults. Use for /setup-pstack, "configure pstack models", "pstack budget", or changing pstack's mode choices.
---

# Setup pstack

Write `~/.devin/rules/pstack-models.md`, an always-applied rule that sets pstack's agent mode per role.

## Steps

### 1. Detect available modes

Enumerate the agent modes you can pass as `devin_mode` to `devin_session_create` in this session. The picker offers: `normal`, `fast`, `fusion`, `lite`, `ultra`, `fast-ultra`. Enterprise policy may remove some; a rejected `devin_mode` produces a 400 that lists the modes actually available. If you cannot detect the available set, offer the full list and let the user drop any they lack. Never write a mode the user does not have. The aliases `inherit-parent` and `auto` are always valid even though they are not modes.

Devin does not expose raw model slugs or vendor choice. Agent modes are the unit of capability and cost; treat them as pstack's "models".

### 2. Load current state

The default role-to-mode mapping is the rule shape shown in step 5 below. If `~/.devin/rules/pstack-models.md` already exists, read it and treat its `# budget` line and its role values as the current choices. Otherwise start from those defaults. A line whose role is not in step 5, such as `hardest tasks`, `architect runners`, `reflect tooling`, or `why investigators`, is from a retired role. Drop it.

A value that is a raw model slug (anything matching `claude-*`, `gemini-*`, `gpt-*`, `grok-*`, or containing a `-high`/`-xhigh`/`-medium`/`-low` token) comes from a Cursor-era rule. Map it by effort before step 3: `xhigh` → `ultra`, `high` → `normal`, `medium` → `fast`, `low` or unmapped → `lite`. In a panel role, each slug maps independently. Tell the user which values you migrated.

Every other value is the user's choice and stays, including every `inherit-parent` or `auto` value.

### 3. Budget, map, and confirm

**(a) Ask for a budget.** Prefer the structured question tool (`user_question` options) over free text. Offer these three options with these exact labels, and name the current budget when the rule records one. With no rule, say that `large` matches the skill defaults.

- `large — ultra default`
- `medium — normal default`
- `small — fast default`

**(b) Apply it.** Build the working table from the skill defaults, and on a re-run keep any role you changed by mode, list, or alias (`inherit-parent`, `auto`). Each role has a target mode, which is its default's mode moved by the budget on the ladder `ultra` > `fusion` > `normal` > `fast` > `fast-ultra` > `lite`. `large` keeps it, `medium` lowers it one step, and `small` lowers it two steps. Most defaults are `ultra`, and each budget label names where those land. `inherit-parent` and `auto` do not change. So `medium` turns `ultra` into `normal`, and `small` turns `ultra` into `fast` and `normal` into `fast-ultra`. If a resulting mode is unavailable to the user, use the available mode closest below it, else mark the role as needing a choice.

**(c) Show the roles and confirm.** Show every role with its mode, marking any unavailable mode as needing a choice. Also list each line step 2 dropped or migrated. Ask whether to accept as-is or change specific roles, offering the detected modes plus `inherit-parent` and `auto` (both mean: this role runs on the parent's agent mode) as the options. Prefer the structured question tool (`user_question` options) over free text. For panel roles (arena runners, interrogate reviewers) the value is a list, and one child session runs per entry, alias entries included, so the list length sets the panel size. `arena cross-judge pool` is also a list, but Arena runs one judge. It tries entries in list order, preferring a mode different from the parent's — order it by preference. An `inherit-parent` or `auto` value never judges. `swarm workers` is the default mode for every worker unless a race or comparison assigns another mode per arm.

### 4. Validate

Every real mode written must be in the detected set. `inherit-parent` and `auto` always pass. If a chosen mode is not available, stop and ask again.

### 5. Write the rule

Write `~/.devin/rules/pstack-models.md` with `alwaysApply: true`, the `# defaults: 3` line, a `# budget` line with the chosen label and the mode that label names, and one line per role, using the same labels poteto-mode uses. Overwrite the whole file so re-runs stay idempotent. Shape:

```
---
description: pstack per-role agent-mode choices (overrides skill defaults)
alwaysApply: true
---
# pstack agent-mode configuration. One line per role. Delete a line to fall back to the skill default.
# `inherit-parent` or `auto` as a value: the role inherits the parent's agent mode (omit `devin_mode`). Alias entries in a panel list still count toward its fan-out.
# defaults: 3
# budget: large (ultra)
feature, refactoring: ultra
bug-fix: ultra
perf-issue: ultra
hillclimb: ultra
judgment and prose: ultra
arena runners: ultra, fusion, fast-ultra
arena cross-judge pool: fusion, normal
swarm workers: normal
interrogate reviewers: ultra, fusion, fast-ultra
```

### 6. Confirm

Tell the user the rule was written and that it applies to new sessions. Re-running this skill updates it.

### 7. Offer a verification skill (optional)

Check whether the project has a way to drive the real app for proof (a `verify-*` skill, or an existing harness). If not, offer once: "want a project-local verification skill, so agents can drive the app the way a user does and prove changes work? I can generate one with /create-verification-skill." On yes, invoke `/create-verification-skill` (resolves wherever pstack is installed: workspace, user, or plugin). On no, move on without pushing.

### 8. Offer a formal verification skill (optional)

Check whether the project already has a formal verification skill (a project skill that runs a model checker, a proof, or another verifier against the code). If not, offer once: "want a formal verification skill, so agents can prove a key property of your code holds in every case in scope, not only the cases tests reach? I can generate one with /create-formal-verification-skill." On yes, invoke `/create-formal-verification-skill` (resolves wherever pstack is installed: workspace, user, or plugin). On no, move on without pushing.
