# Playbook 04 — CI digest

## When to use it

A check went red on my PR and the Actions log is a few thousand lines. I want the failing step and the lines that matter.

## Prompt to send

```
ci https://github.com/cataclysmbn/Cataclysm-BN/pull/<number>
```

## Expected output

1. Failed checks — name, conclusion, link.
2. Relevant log lines — only lines actually fetched. If logs need auth, the agent says so instead of guessing.
3. One suggested next step.

If everything is green: a single line with the head SHA.

## What I do with it

Fix locally, push, run it again only if the next run is also red.

## Worked example

<!-- paste real output from Agent37 run here -->
