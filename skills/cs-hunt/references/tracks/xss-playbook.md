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
2. `## Domain Fit`
3. `## Enumeration`
4. `## Reviewed Surfaces`
5. `## Findings`
6. `## Reviewed And Found Safe`
7. `## Hardening Notes`
8. `## Trust-Model Notes`
9. `## Validation Notes`
10. `## Why This Step Stayed Quiet` when there are no material findings

`## Findings` should distinguish:

- `confirmed`
- `strong-code-evidence`
- `needs-runtime-validation`
