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

Run on 5 Oct 2026. Screenshot: [evidence/02.jpg](../../evidence/02.jpg). Raw output, unedited:

## Repro checklist
1. Enable the “Disable NPC Needs” mod (`NO_NPC_FOOD`) in a world.
2. Recruit an NPC follower.
3. Talk to them and press **[S] Size up stats**.
4. Observe that the display says “Hungry” even though the NPC is fully fed.

## Likely files and functions
- `src/npctalk.cpp` — size-up info hunger branch — named in the issue; it contains the reported inverted condition.

## Minimal fix plan
- Invert the hunger-deficit condition so fed NPCs do not display “Hungry.”
- Check the hunger time estimate’s units; the issue notes `bmr()` is kcal per day.
- Test the display for fed and hungry NPCs, including with `NO_NPC_FOOD` enabled.

## What could go wrong
Changing the threshold or time calculation could make the display inconsistent with hunger complaints or other NPC hunger logic. The issue doesn’t establish whether this calculation is duplicated elsewhere. Comments could not be fetched: the comments endpoint returned HTTP 403 rate-limited.

