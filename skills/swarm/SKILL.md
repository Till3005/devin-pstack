---
name: swarm
description: "Fan out N parallel workers, drain them, and return one report. Use for /swarm, 'swarm this', or parallel coverage, races, gauntlets, and exploration."
---

# Swarm

## Start

Open a todolist with one entry per phase before launching anything.

1. Frame
2. Fan out
3. Aggregate
4. Report

## Phase A: Frame

1. State the done predicate and the artifact or report the swarm must return.
2. Choose the shape. Partition into slices, race N workers on identical briefs, or mix both. For a race or mixed shape, declare `first pass`, `rank all`, or `best-of` before spawning.
3. Pick the worker mode from the `swarm workers` line in `~/.devin/rules/pstack-models.md`. If the rule or that line is missing, use `normal`. For `auto` or `inherit-parent`, omit `devin_mode` so the workers run on the parent's agent mode. If `devin_session_create` rejects a mode (its 400 lists the modes the user has), use the default and say so; if it rejects the default, use the closest available mode below it on the ladder `ultra` > `fusion` > `normal` > `fast` > `fast-ultra` > `lite`. For a mode race, name each arm's mode up front. Pick each worker's platform when the task is platform-sensitive.
4. Choose the execution engine by size. For one to four workers, one `devin_session_create` call with `devin_mode` at batch level and `notify_on_response: true` on every spec. For five or more workers, a race you may rerun, or any pipeline, write a dynamic workflow and `run_workflow` it — it journals each agent result, so a crash resumes instead of respawning the swarm. Use `run_subagent` only when a worker must share this machine's checkout or processes.
5. Give each worker its own writable output when it writes. A Devin child session already has its own VM and checkout; for same-VM subagents, give each its own worktree or output directory. When workers verify or measure commits, each brief names the exact SHAs. A measurement brief also names the method (sample count, what one sample is, order). The worker records both in its result.

## Phase B: Fan out

Spawn all N workers in one call, each spec with `notify_on_response: true` and the step 3 mode at batch level. Children run on their own VMs by default — there is no "local" flag; same-machine help is `run_subagent`.

When a worker must start from a non-default pushed branch, name the branch in its prompt and have it check out that branch first.

Every brief stands alone. Include the goal, scope, exact slice or race arm, how to verify, and what to report. Reports use `PASS`, `ISSUES`, or `BLOCKED` with evidence. A worker that can prove a defect reports `ISSUES` and lists every issue it can prove, not only the first.

If a worker drops out, proceed with N-1 and note it.

## Phase C: Aggregate

Read the settled sessions' results (`devin_session_interact` get, or structured output if the briefs declared a schema). Drop a result that does not record the SHAs and method its brief names, and respawn that worker once. After a second miss, record a gap. A gap does not count as a pass. For coverage, every required slice needs a result. For a race, apply the selection rule declared up front. Use first pass, rank all, or best-of. Do not paste raw worker dumps.

## Phase D: Report

Return one consolidated in-chat report with a result table, issue one-liners, gaps or dropouts, and the race rule when used.
