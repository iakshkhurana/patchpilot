# Playbook 02 — reproduction checklist

## When to use it

After picking an issue from triage, before touching code. The point is to not spend 40 minutes reading a long thread when the agent can collapse it into steps.

## Prompt to send

```
repro https://github.com/cataclysmbn/Cataclysm-BN/issues/<number>
```

## Expected output

Four sections, in order:

1. Numbered repro checklist (or `insufficient data` plus what's missing).
2. Likely files and functions, each tagged `(guess)` unless the issue named it.
3. Three-bullet minimal fix plan.
4. One paragraph on what could go wrong.

Under 500 words.

## What I do with it

Follow the checklist on a local build. If the bug reproduces, the file list becomes my starting `grep`. If it doesn't, I comment on the issue myself asking for the missing step — the agent never posts.

## Worked example

<!-- paste real output from Agent37 run here -->
