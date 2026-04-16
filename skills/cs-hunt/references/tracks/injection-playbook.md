# Injection Playbook

## Look For

- raw SQL and query structure control
- shell execution
- unsafe file path joins or traversal
- unsafe deserialization
- template execution on untrusted input

## Ask

- does user input ever shape the structure of a query, shell command, file path, template, or deserializer input?
- are guards contextual, or just generic cleaning?
- does a later concat undo an earlier safety measure?

## False-Positive Traps

- parameterized query APIs that only look scary in grep
- tooling-only shell commands that are never hit from an app path
- sanitization that is actually contextual and sufficient

## Baseline Searches

- `exec\\(`
- `execSync\\(`
- `spawn\\(`
- `child_process`
- `subprocess`
- `os.system`
- `popen`
- `\\.query\\(`
- `\\.raw\\(`
- `path\\.join\\(`
- `filepath\\.Join\\(`
- `eval\\(`
- `new Function`
- `yaml\\.load\\(`
- `pickle\\.loads`

## Output Shape

### `hunt/injection.md`

Required sections:

1. `# Injection`
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
