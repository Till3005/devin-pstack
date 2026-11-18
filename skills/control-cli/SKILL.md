---
name: control-cli
description: Drive and verify a CLI or TUI as a user would. Use when verifying command-line behavior, flags, output, exit codes, or interactive terminal flows — not just that the code compiles.
---

# Control CLI

A claim about CLI behavior is unverified until someone runs the command. In Devin:

1. **Run it for real** — `exec` with the actual invocation a user would type. Check stdout, stderr, and the exit code — all three.
2. **Interactive TUIs** — `exec` with `tty: true` and a persistent `shell_id`; drive it with `write_to_process` (arrow keys, Enter, Ctrl+C via `<UP>`, `<CR>`, `<C-c>`). Read output with `get_output`.
3. **Edge lanes** — missing args, `--help`, bad input, non-zero exit paths. Verify the error text a user would actually see.

Rules:

- The real binary or entry point, not a test double — unless you name the double.
- Report the command line you ran and the observed output verbatim when claiming a behavior.
- Timing-sensitive flows need `wait_for` patterns or `get_output` timeouts, not guesses.
- If a path cannot run in this environment (missing credentials, OS-specific), say what is unverified instead of implying it passed.
