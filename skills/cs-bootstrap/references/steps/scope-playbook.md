# Scope Playbook

Write `bootstrap/scope.md` first. This file decides how later phases should treat the target instead of letting hunt guess the repo shape.

This is the first bootstrap checkpoint. Gather only the evidence needed to classify the target, choose runtime posture, and name the early coverage limits. Write `scope.md` before moving on to `inventory.md`.

## What To Decide

- What is the target in practice: web app, API, supporting service, CLI-first system, background worker, library, or mixed repo?
- Which directed hunt tracks are fully applicable, partially applicable, or largely inapplicable?
- Which runtime posture fits best: `code-only`, `read-only runtime`, or `local runtime`?
- What is the biggest blocker if the posture is not `local runtime`?
- If the posture is `local runtime`, what is the narrowest reasonable startup or smoke-check path bootstrap should attempt before hunt?
- What working-copy or safety constraints should later phases honor?
- What blocking assumptions or coverage limits should be called out early?

Working-copy policy should be explicit when it matters:

- whether later phases must avoid source-repo virtualenvs or package installs
- whether copied runtime residue should be treated as sensitive
- whether local runtime should rebuild its own environment inside the working copy

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

`## Runtime Posture` should name not just the chosen posture, but also the intended next runtime action:

- for `local runtime`: the startup or smoke-check path bootstrap should attempt before hunt
- for non-`local runtime`: the main blocker that prevented that attempt
