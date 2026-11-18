### Autonomous run

**You own the exit condition. Define done, then drive to it without stopping.**

1. State the exit condition as a checkable predicate before the first iteration (tests green, repro fixed, all N PRs merged, pixel-diff zero).
2. Pick the wake mechanism — Devin's native wake primitives, which survive sleep and restarts: `devin_reminder_manage` fires this session on an RRULE (set `COUNT`/`UNTIL` so it ends), a PR attached via `git_take_over_pr` wakes you on comments, CI changes, and pushes, `git_pr_checks` waits inside the turn for checks or a named stage, and `devin_automation_manage` runs a durable scheduled or event-triggered automation. An event to watch (CI, a merge, a ref advancing) gets the takeover/notification path or a watcher child session that wakes you on the event, with a long reminder heartbeat as fallback. No event gets a fixed-interval reminder sized to when the result is worth re-checking. Delete each reminder once its job is done. Pick each child's agent mode for the job, per the Subagents section.
3. Each iteration makes the smallest change the evidence justifies, verifies it against the predicate, commits if it advanced, discards changes that didn't help. Belt-and-suspenders that "might help" gets reverted, not left to ride.
   Sequence the work via the **sequence-verifiable-units** principle skill, verifying each unit before the next instead of batching checks at the end.
4. Mid-run discoveries that support the goal are yours. Address broken skills, related bugs, flaky verifiers, review noise, tooling failures, orphaned follow-ups, and fixable drift yourself via poteto-mode. Put out-of-band fixes in their own PR. Do not park reversible work for the human or use the structured question tool. Surface only irreversible actions, genuine product or preference calls no experiment can settle, or a real dead end. Keep the predicate as the main drive, and return to it after each side fix.
5. Checkpoint every iteration via the **show-me-your-work** skill, a row for what changed and whether the predicate moved.
6. Stop when the predicate is met. A plateau is not a stop, so keep going and pivot your approach to push past it. Surface a genuine dead end rather than spinning, and never relax the predicate to declare victory.

**Reply:** the exit condition, iterations run, what landed, what was discarded, final predicate state.
