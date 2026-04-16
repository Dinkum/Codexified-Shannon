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
2. `## Summary`
3. `## What We Looked Into`
4. `## Issues We Found And Verified`
5. `## General Concerns And Hardening Suggestions We Verified`
6. `## Misc Notes`

All sections should be flat lists.

Also maintain:

- `hunt/artifacts/injection/candidates_log.md`
- `hunt/artifacts/injection/verified_log.md`
