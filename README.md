# PatchPilot

A small AI copilot I run on an Agent37 sandbox while contributing to `cataclysmbn/Cataclysm-BN`. It ranks open issues worth picking up, turns a long issue thread into a repro checklist, drafts replies to PR reviews, and tells me which CI step actually failed. It reads GitHub; it never writes to it.

## Why I built it

I'm contributing to Cataclysm-BN for the ByteAsk challenge during Hackyard Build 2026. The repo has hundreds of open issues and I kept losing the first half hour of every session scrolling through them. Agent37 gives a free sandbox with a tiny model budget (about $0.20 of default-model credit and 10 awake-hours a week), so a chatty, multi-turn assistant was out. What fit the budget was a set of four strict commands with fixed output shapes: ask once, get a table, move on. That's PatchPilot. The agent's outputs feed straight into the PRs I'm actually writing, which is also why the evidence in this repo is real and dated.

## How it works

```
        one-line command                 GET only, unauthenticated
 me  ───────────────────▶  Agent37 sandbox  ─────────────────────▶  api.github.com
     ◀───────────────────   (PatchPilot)    ◀─────────────────────
        table / checklist /                  issues, comments,
        draft reply / CI digest              PRs, check-runs
          │
          ▼
   my local clone of Cataclysm-BN
   (I do the editing, commits, replies, pushes)
```

The agent is a system prompt ([agent/instructions.md](agent/instructions.md)) pasted into a stock Agent37 coding agent. No extra tooling, no webhooks, no tokens. Everything it knows about an issue comes from one API pass per command.

## The four playbooks

- [01 — issue triage](agent/playbooks/01-issue-triage.md): `triage owner/repo` → ranked top-8 table using a 7-point rubric.
- [02 — repro checklist](agent/playbooks/02-repro-checklist.md): `repro <issue-url>` → steps, likely files, 3-bullet fix plan, risks.
- [03 — PR review reply](agent/playbooks/03-pr-review-reply.md): `reply <pr-url>` → reviewer points plus a first-person draft I edit before posting.
- [04 — CI digest](agent/playbooks/04-ci-digest.md): `ci <pr-url>` → failed checks, the real log lines, one next step.

### The triage rubric

This is the part I'd defend as a design choice rather than a prompt. Each issue gets:

| Signal | Points | Reason |
|---|---|---|
| No assignee | +2 | Nobody's on it |
| No linked PR | +2 | Nobody's half-done with it either |
| Repro steps present | +1 | I can verify before I touch code |
| Touched files guessable from text | +1 | Shorter path from issue to `grep` |
| Activity in last 30 days | +1 | Maintainers still care |

Max 7. It's biased toward issues I can finish, not issues that are important. That's deliberate for a first-time contributor on a deadline.

## Real results

Every row here links to a sanitized copy of the actual output in [examples/](examples/) and a screenshot in [evidence/](evidence/). Rows are added as I run things, not before.

| Date | Command | Target | What it produced | Output |
|---|---|---|---|---|
| 2026-10-05 | `triage` | cataclysmbn/Cataclysm-BN | Top-8 table, all scored 7/7. 174 unique issues fetched; linked-PR check hit the 60/hr rate limit on 36 of them and said so. | [examples/01-triage.md](examples/01-triage.md), [screenshot](evidence/01.jpg) |
| 2026-10-05 | `repro` | [#10447](https://github.com/cataclysmbn/Cataclysm-BN/issues/10447) | 4-step repro, pointed at `src/npctalk.cpp`, 3-bullet fix plan. Comments endpoint was rate-limited and it reported that instead of guessing. | [examples/02-repro-10447.md](examples/02-repro-10447.md), [screenshot](evidence/02.jpg) |
| | | | | |

How to add a row: run the command on Agent37, screenshot the response into `evidence/`, paste the raw text into `examples/NN-command.md`, fill the playbook's worked-example placeholder, write a devlog line, then add the row.

## Running it yourself

1. Sign in to Agent37 and launch the free coding agent.
2. Open the agent's chat, paste the full contents of [agent/instructions.md](agent/instructions.md) as its instructions.
3. Send one playbook prompt per message, e.g. `triage cataclysmbn/Cataclysm-BN`.

Budget tips that made the free tier workable for me:

- One command per message. Never "also could you...".
- Don't ask it to retry. If an API call fails it reports the status code and stops; decide yourself whether to re-send.
- `repro` is the most expensive command (it reads comments). Run `triage` first so you only `repro` issues you'll actually take.
- Shut the agent down between sessions; awake-hours are the real limit, not tokens.

## Limitations

- Unauthenticated GitHub API: 60 requests per hour per IP. A `triage` uses 2 to 3, a `repro` 2 to 4, so this is fine for a session but not for a loop.
- Read-only by design. It cannot comment, label, or push, so a bad output costs me a minute, not a maintainer's time.
- The model can misread long review threads. The `reply` draft is a draft; I rewrite anything that sounds off.
- Linked-PR detection relies on the timeline endpoint and "Fixes #" patterns. It will miss PRs that reference an issue only in a commit message.
- Scores are only as good as the issue text. A well-written duplicate outranks a badly written real bug.

## Built for Hackyard Build 2026 x Agent37

Built and used during Hackyard Build 2026 as part of the Agent37 challenge, on the Agent37 free tier. MIT licensed.
