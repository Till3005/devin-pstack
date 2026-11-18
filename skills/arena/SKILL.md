---
name: arena
description: "Spawn N parallel candidates at the same task, pick a base, graft the strongest parts of the losers into it. Use for /arena, 'arena this', 'throw it in the arena', or when one attempt at a non-trivial artifact would lock in the wrong shape."
disable-model-invocation: true
---

# Arena

Open a todolist with one entry per phase (Frame, Fan out, Cross-judge, Pick, Graft, Verify) before launching anything.

## Phase A: Frame

Every candidate gets the same prompt, so the prompt is the contract.

1. State the artifact each candidate is producing.
2. Derive the rubric, 3-6 concrete gradeable criteria for what success looks like on *this* task. It is the picker's tool in Phase D. Candidates only see the task.
3. Pick the runners from the `arena runners` line in `~/.devin/rules/pstack-models.mdc`, by default `ultra`, `fusion`, and `fast-ultra`. An `auto` or `inherit-parent` entry is the parent's agent mode, so omit `devin_mode` for that seat. Devin does not expose model slugs or vendor choice; mode diversity is the spread you get — note that. If `devin_session_create` rejects a mode, use the nearest available mode on the ladder `ultra` > `fusion` > `normal` > `fast` > `fast-ultra` > `lite` or drop the seat, keep at least two runners, and say so. Spawn more for multiple design directions, and repeat one mode when the work is generation-bound, not judgment-sensitive.
4. Give each candidate its own output location. A Devin child session has its own VM and checkout, so isolation is automatic; for same-VM subagents use a git worktree or `/tmp/arena-<n>/`, per the **separate-before-serializing-shared-state** principle skill.

## Phase B: Fan out

Spawn all N child sessions in one `devin_session_create` call with `notify_on_response: true` on every spec, each with the task, the path to the shared grounding, its own output path, and instructions to produce the artifact and a short rationale naming the alternatives it considered and what it rejected. Set `devin_mode` at batch level when runners share a mode; for mixed modes, spawn one batch per mode.

If a candidate produces no output, proceed with N-1 and note the dropout.

## Phase C: Cross-judge

After all Phase B candidates settle, spawn one judge child session from the `arena cross-judge pool` line in `~/.devin/rules/pstack-models.mdc`, by default `fusion`, `normal`. Try entries in list order, preferring a mode different from the parent's. An `auto` or `inherit-parent` entry is the parent's mode and never judges. If you do not know the parent's mode, use the first real entry and say that the diversity check did not run. Devin has no vendor axis; a different mode is the available diversity. While you read in Phase D, the judge scores each candidate, by path label, on each rubric criterion and recommends a base. Give it read-only scope: have it report verdicts, not edit.

## Phase D: Pick a base

Read every candidate end to end before picking. Score each against the rubric criterion by criterion, not on holistic feel, and compare with the cross-judge when Phase C ran one. Agreement on the base confirms the pick. Disagreement means one of you is biased or the rubric was ambiguous. Read both rationales before deciding.

Pick the base a future maintainer can extend most easily without breaking invariants. When two feel tied, prefer the cleaner boundary or smaller API, per the Laziness Protocol.

## Phase E: Graft

Walk each losing candidate once more for what is worth porting into the base, usually one or two things per candidate, not most of it. Fold each graft in by hand, per the **redesign-from-first-principles** principle skill, so the result stays coherent under one mental model. Don't paste mechanically.

If the candidates converge on one shape, that is strong agreement. Note it and ship the consensus shape. No graft is needed. If they wildly diverge, Phase A was under-specified. Reframe and re-run rather than averaging the divergence.

## Phase F: Verify

Verify the synthesized artifact like any other output, per the **prove-it-works** principle skill. If verification finds a problem the arena missed, either Phase A was wrong (re-frame and re-run) or a candidate caught it and you missed the graft (go back to Phase E). Don't paper over it.

## Outputs

One synthesized artifact, with a short synthesis note alongside naming the base and why, the cross-judge's verdict or that Phase C skipped the judge, each graft with its source candidate, what was rejected and why, any convergence or dropouts, and the verification result.
