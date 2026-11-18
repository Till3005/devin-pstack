---
name: control-ui
description: Drive and verify a real UI — browser, Electron, or web app — as a user would. Use when verifying UI changes, reproducing UI bugs, or proving a user-facing flow works, not just that code compiles.
---

# Control UI

A claim about UI behavior is unverified until someone drives the interface. Prefer evidence gathered by driving the app, in this order:

1. **Devin's testing agent** — for end-to-end flows, hand off: it owns dev servers, login, and produces an annotated recording plus assertions. Give it the exact behavior to verify and the starting state.
2. **The computer tool** — for a focused check you can do faster yourself: open the app in the running Chrome, use `inspect`/`query`/`act` accessibility actions first, screenshots for visual assertions only.
3. **Browser console** — for DOM/CSS assertions (`browser_console`) when layout or computed style is the claim.

Rules:

- Drive the real surface. A green build, passing unit test, or code read is not a UI verdict.
- State the precondition before the assertion ("with sidebar expanded, the toggle collapses it").
- Check the failure lanes too: empty state, error toast, loading, narrow viewport.
- If you cannot drive it (missing login, external service), say exactly what is unverified instead of implying it passed.
