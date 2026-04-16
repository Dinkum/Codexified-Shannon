# Scope Playbook

Write `bootstrap/scope.md` first. This file decides how later phases should treat the target instead of letting hunt guess the repo shape.

## What To Decide

- What is the target in practice: web app, API, supporting service, CLI-first system, background worker, library, or mixed repo?
- Which directed hunt tracks are fully applicable, partially applicable, or largely inapplicable?
- Which runtime posture fits best: `code-only`, `read-only runtime`, or `local runtime`?
- What is the biggest blocker if the posture is not `local runtime`?
- What working-copy or safety constraints should later phases honor?
- What blocking assumptions or coverage limits should be called out early?

Be explicit when the repo is not a classic web application. A good scope file prevents later phases from pretending exploitability that the target shape does not support.

## Output Shape

### `bootstrap/scope.md`

Required sections:

1. `# Scope`
2. `## Target Classification`
3. `## Directed Hunt Track Fit`
4. `## Runtime Posture`
5. `## Working Copy Policy`
6. `## Blocking Assumptions`

`## Blocking Assumptions` should be concrete and report-ready. It is the main bootstrap source for `Context (Bootstrap/Meta)` -> `What We Could Not Check`.
