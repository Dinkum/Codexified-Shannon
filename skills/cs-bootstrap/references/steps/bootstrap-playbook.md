# Bootstrap Synthesis Playbook

Use this step to turn `inventory.md` and `recon.md` into clear hunt direction.

## What Good Synthesis Looks Like

- Point hunt at the highest-reward paths first.
- Separate real hotspots from noisy but low-value areas.
- Call out likely false trails so hunt does not waste time rediscovering them.
- Name pressure points where one weak assumption could break an important boundary.
- Pull forward the static pressure points that matter most so hunt does not have to rediscover them.
- Pull forward the highest-value hardening leads so they survive even when no later step turns them into confirmed findings.
- Leave a crisp starting point so hunt can begin without re-summarizing bootstrap.

## Writing Tips

- Prefer concrete repo-specific directions over generic advice.
- Anchor each direction to a file, route, helper, service, or config path.
- When a likely chain matters, name the two or three parts that would combine.
- Keep this file short enough that a human or model can skim it and immediately know where to start.

## Output Shape

### `bootstrap/bootstrap.md`

Required sections:

1. `# Bootstrap`
2. `## High-Reward Investigation Directions`
3. `## Likely Hotspots`
4. `## Likely False Trails`
5. `## Likely Pressure Points Or Chains`
6. `## Hardening Priorities`
7. `## Hunt Starting Point`

This file is the operator-facing bootstrap synthesis.

`## Hardening Priorities` should be concise and report-ready so the final `## Hardening` section can pull from it directly.
