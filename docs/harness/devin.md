# Devin harness notes

pstack was written for Cursor. On Devin, skills keep their names — call them by name (`poteto-mode`, `unslop`, `deslop`, ...) or ask the agent to work in "poteto mode". Read this file once, then map Cursor names with the tables below. `<pstack>` means the folder holding `skills/` and `docs/` — installed plugins live under your Devin plugin store; you can also `git clone` this repo and read files directly.

## Install

Tell Devin in any session: **"install the plugin from `github.com/Till3005/devin-pstack`"** — Devin loads `plugin.json` + `skills/` from the repo root and the skills appear in your catalog. For every member of an org, install at account scope.

## Spawning delegates

| Cursor | Devin |
|---|---|
| Task subagent / `poteto-agent` | A Devin **child session** (`devin_session_create`). Put `agents/poteto-agent.md`'s body into the child's prompt, plus the `poteto-mode` skill name and this repo path. One fresh child per task; resume via `devin_session_interact` message only in the cases the Subagents section names. |
| Cloud subagent | Child sessions already run on their own VM — that *is* the isolation. |
| `/in-cloud` prefix | Unneeded — say "spawn a child session for this". |

## Modes and models

| Cursor | Devin |
|---|---|
| `model:` slug in agent frontmatter | Devin **agent modes**: Normal, Fast, Fusion, Lite, Ultra, Ultra Fast. Map poteto roles: code delegates → Normal/Fast, judgment/judge panels → Ultra/Fusion, verification lanes → Normal. Pass the mode at spawn, not a model name. |
| Custom Mode (skill pinned per turn) | Plugin skills stay available every turn automatically; ask once to "work in poteto mode" and cite it in kickoff prompts. |
| `/deslop`, `/control-ui`, `/control-cli` from cursor-team-kit | Bundled here as `deslop`, `control-ui`, `control-cli` skills (Devin-native rewrites). |

## Loops and overnight work

| Cursor | Devin |
|---|---|
| `/loop` wake mechanism | `devin_reminder_manage` (self-wake on a schedule) or a Devin **automation** (`devin_automation_manage`) for recurring/event-driven runs. |
| Cursor Project / persistent coordinator | A long-running Devin session or a Bot owns the plan; it spawns child sessions per work unit. |
| Cursor Automations (benny) | Recreate via `devin_automation_manage` — the benny prompts in `automations/benny/templates/` are reusable verbatim as automation session prompts. |

## Files and state

| Cursor | Devin |
|---|---|
| `~/.cursor/rules/pstack-models.mdc` | `~/.devin/rules/pstack-models.md`, or a Devin **rule** in your plugin store. |
| `.cursor/skills/`, `~/.cursor/skills/` | `.devin/skills/`, plugin skills via `manage_plugin`. |
| Transcripts in `~/.cursor/projects/...` | Session history via `devin_session_interact` / `devin_session_events`. |

## PR and verification loops

| Cursor | Devin |
|---|---|
| Babysit a PR | `git_take_over_pr` + `git_pr_checks` for live CI/comment wakeups. |
| Verifier driving the app | `control-ui` / `control-cli` skills, or hand off to Devin's testing agent for recorded proof. |

## Where Devin exceeds Cursor for pstack

Native advantages worth leaning on:

- **True VM isolation per subagent** — each `devin_session_create` child gets its own machine and checkout; no shared-worktree hazards, no cross-lane file races.
- **Cross-platform fan-out** — the `fleet` playbook verifies a change on `windows`/macOS/linux children in one fan-out; Cursor cloud agents have no platform axis.
- **Live PR wakeups, no polling** — `git_take_over_pr` streams comments, CI changes, and pushes into the session; `git_pr_checks` waits in-turn. Replaces Cursor's `watch-pr` script polling under `/loop`.
- **Devin Review triage loop** — `devin_review_manage` verdicts post to the PR via `end_triage`; a structured upgrade over Bugbot comment scraping.
- **Dynamic workflows** — `run_workflow` scripts fan out ≥5 lanes with journals and resume; survived restarts out of the box.
- **Durable automations** — `devin_automation_manage` triggers on schedule/webhook/Slack/GitHub/Linear/Jira with human approval cards; benny ports onto this directly.
- **Environment snapshots + blueprint testing** — `devin_blueprint_test` verifies setup in a clean VM; `update_environment_config` persists it.
- **Session search and insights** — `devin_session_search`/`devin_session_insights` find and analyze prior runs; richer than transcript globbing.
- **Boards, billing tags, oncall** — Devin Boards claim tickets for child sessions; billing tags attribute cost; oncall responders triage Slack/PagerDuty.
