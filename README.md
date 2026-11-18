# devin-pstack

poteto's pstack engineering skills, ported to Devin — install once, agents work in her style.

## Install

In any Devin session:

```text
install the plugin from github.com/Till3005/devin-pstack
```

Then run `/setup-pstack` once to map roles to agent modes, and start work with `/poteto-mode`.

## What's inside

- **64 skills** — poteto-mode playbook runner, swarm/arena fan-outs, interrogate, deslop, control-ui/control-cli, principles, and more, all rewritten to Devin mechanisms (child sessions, `devin_mode`, reminders, automations, Devin Review triage, `git_*` tools).
- **Playbooks** — shipping, babysit, autopilot, bug-fix, fleet (cross-platform verification), and more under `skills/poteto-mode/playbooks/`.
- **Agents** — `poteto-agent` and `comment-sicko` briefs for embedding in child-session prompts.
- **Automations** — the `benny` Slack triage/repro pack, ported to `devin_automation_manage`, under `automations/benny/` (copy it into a target repo to use).
- **Docs** — `docs/guide/` walkthroughs and `docs/harness/devin.md`, including a "where Devin exceeds Cursor" section.

Ported from Lauren Tan's pstack (Cursor plugin, MIT). Upstream: https://github.com/cursor/plugins/tree/main/pstack
