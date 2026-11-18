### Fleet

**You own the matrix. One change, verified on every platform it must run on, by sessions that live there.**

A Devin capability with no Cursor analog: child sessions can target different VM platforms, so cross-platform verification is native fan-out, not a local matrix of VMs you babysit.

1. **Define the matrix.** Name each target: `platform` values the org has registered (`linux`, `windows`, a macOS platform label, or an outpost pool name). Omit `platform` on a spec that should inherit your own.
2. **Fan out one verifier per platform** via `devin_session_create`, same prompt text, `platform` per spec, `notify_on_response: true` on each. Pin the exact ref or patch to verify so every lane checks the same thing.
3. **Collect and compare.** Read each settled child's `structured_output` or final message with `devin_session_interact` `action="get"`. A platform-specific failure is a finding on the platform, not a reason to re-run the fleet; a failure on every lane is a bug in the change.
4. **Iterate per platform.** Fix, then re-run only the lanes that failed — `devin_session_interact` `action="message"` resumes the same child on the same VM with its checkout intact, no re-provisioning.

For ≥5 lanes, dependent stages, or resumable runs, use a dynamic workflow (`run_workflow`) instead of raw fan-out — agent results replay on resume.

**Reply:** the matrix, each platform's verdict, any platform-specific findings, and what remains unverified.
