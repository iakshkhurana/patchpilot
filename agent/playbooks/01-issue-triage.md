# Playbook 01 — issue triage

## When to use it

Start of a contribution session, or whenever the current issue stalls and I need the next one. Run it once; the result is good for a few days.

## Prompt to send

```
triage cataclysmbn/Cataclysm-BN
```

One line, nothing else. Swap the repo slug if needed.

## Expected output

A markdown table, top 8 issues, columns: rank, issue link, score (0–7), labels, one-line reason. Then a single line with fetch counts (total issues, how many `bug`, how many `good first issue`). Should come in under 300 words.

Scoring is fixed in `agent/instructions.md`: no assignee +2, no linked PR +2, repro steps +1, guessable files +1, activity in last 30 days +1.

## What I do with it

Open the top 3 in the browser, sanity-check that nobody claimed them in a comment the API scorer missed, pick one, then run `repro` on it.

## Worked example

<!-- paste real output from Agent37 run here -->
