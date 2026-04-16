# XSS Playbook

## Look For

- server-rendered HTML sinks
- client-side HTML injection
- markdown and rich-text rendering
- user-controlled content returning to a browser context

## Ask

- where does attacker-controlled content return to a browser context?
- what is the exact render context?
- is the encoding matched to that context, or merely present?

## False-Positive Traps

- escaped template output mistaken for raw HTML
- reflection into a non-executable context
- developer or test pages not served in the target runtime

## Baseline Searches

- `innerHTML`
- `outerHTML`
- `dangerouslySetInnerHTML`
- `v-html`
- `res\\.render\\(`
- `render_template`
- `TemplateResponse`
- `markdown`

## Output Shape

### `hunt/xss.md`

Required sections:

1. `# XSS`
2. `## Summary`
3. `## What We Looked Into`
4. `## Issues We Found And Verified`
5. `## General Concerns We Found And Verified`
6. `## Misc Notes`

All sections should be flat lists.

Also maintain:

- `hunt/artifacts/xss/candidates_log.md`
- `hunt/artifacts/xss/verified_log.md`
