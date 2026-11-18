# Changelog

## [0.15.44+devin.4]

### Fixed
- **Critical**: removed `disable-model-invocation: true` from all 57 skill manifests. On Cursor this made skills user-invocable only; on Devin it makes them unreachable entirely (not invocable by the model OR by name). Verified live: after install only 4 of 61 skills were callable. Name-mention invocation (`/swarm`, "use interrogate") is Devin's equivalent of Cursor's slash commands, so all skills must remain invocable.


All notable changes to this port. Versioning follows upstream pstack; port revisions are marked with `+devin.N`.

## [0.15.43+devin.3]

### Changed
- Storefront README: hero, badges, full skill catalog, Devin-vs-Cursor comparison table, honest status section.
- Logo redrawn in Devin's official 2D mascot style (flat vector, uniform outline).

### Fixed
- Skill count corrected to 61 core + 3 benny-pack skills.
- `.devin/rules/*.md` naming unified (`.mdc` was a Cursor extension convention).

## [0.15.43+devin.2]

### Changed
- Full real-session audit: every Cursor-specific mechanism replaced with a native Devin equivalent.
  - `~/.cursor/projects/*/agent-transcripts` mining → `devin_session_search` / `devin_session_events` / `devin_session_interact`.
  - `watch-pr` polling scripts → `git_take_over_pr` live notifications + `git_pr_checks` wait modes; dead script set deleted.
  - `/loop` → `devin_reminder_manage` (RRULE) and `devin_automation_manage` for recurring runs.
  - Bugbot triage → `devin_review_manage` structured triage loop.
  - `AskQuestion` → `message_user` structured questions; `request_secret` for secrets.
  - `origin pr` → `gh`; `.cursor/` paths → `.devin/`.
- `fleet` playbook added: cross-platform verification fan-out to Windows/macOS/Linux child sessions.
- `docs/harness/devin.md` documents where Devin exceeds upstream.

## [0.15.43+devin.1]

### Added
- Initial deep port of [pstack](https://github.com/cursor/plugins/tree/main/pstack) v0.15.43 to Devin.
- `plugin.json` (agent-plugins.org schema), MIT license retained, upstream attribution.
- 61 skills, 2 agent briefs, benny automation pack, 10-part guide.
