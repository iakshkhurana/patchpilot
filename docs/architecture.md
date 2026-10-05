# Architecture

PatchPilot is a system prompt and four conventions. There is no code to deploy. This page is about the decisions behind that.

## Where it runs

An Agent37 sandbox: an isolated container with a coding agent in it, started from the dashboard. The sandbox has outbound network access, which is all PatchPilot needs. Nothing from my machine is mounted in; nothing it produces lands anywhere except the chat response. If the agent misbehaves, the blast radius is one chat window.

## Why it's read-only on GitHub

The agent has no GitHub token. Every request is an unauthenticated `GET` to `api.github.com`. This was the first decision and the rest follow from it:

- A hallucinated reply can't be posted. I copy, edit, and post myself.
- No token means no scope to leak from a sandbox I don't fully control.
- Maintainers of Cataclysm-BN never interact with a bot. They interact with me.

The cost is the 60 requests/hour rate limit. For single-command use that's enough; see the cost table below.

## Data flow per command

| Command | API calls | What's read | What comes back |
|---|---|---|---|
| `triage owner/repo` | `GET /repos/:o/:r/issues?labels=bug`, same with `good first issue`, optionally `/issues/:n/timeline` for linked-PR checks on the top candidates | Title, body, labels, assignees, `updated_at` | Ranked top-8 table + counts |
| `repro <issue>` | `GET /repos/:o/:r/issues/:n`, `GET .../comments`, at most 2 `GET /contents/:path` | Body, comments | Checklist, file guesses, 3-bullet plan, risk note |
| `reply <pr>` | `GET /pulls/:n`, `/pulls/:n/comments`, `/pulls/:n/reviews`, `/issues/:n/comments` | Review threads | Reviewer points + first-person draft |
| `ci <pr>` | `GET /pulls/:n`, `GET /commits/:sha/check-runs`, `GET /commits/:sha/status` | Check names, conclusions, annotations | Failed checks, log lines, one next step |

Nothing is cached between messages. Each command is stateless by design so a cold sandbox behaves the same as a warm one.

## Cost model on the free tier

Free tier at the time of writing: roughly $0.20 of default-model credit plus 10 awake-hours per week. Rough per-command token estimates, based on the output caps in the instructions and typical Cataclysm-BN issue sizes:

| Command | Input (approx.) | Output (cap) | Notes |
|---|---|---|---|
| `triage` | 15k to 30k tokens (100 issues x 2 labels, bodies truncated by the model) | ~400 | Biggest input; run once per session |
| `repro` | 3k to 10k | ~500 | Comments dominate; long threads cost more |
| `reply` | 2k to 6k | ~500 | Cheap unless the review has 20+ threads |
| `ci` | 1k to 3k | ~200 | Cheapest |

The system prompt itself is about 1k tokens and is sent every turn, which is another reason to keep it to one command per message. Awake-hours burn whether or not I'm sending messages, so I stop the agent as soon as I have the output I need.

## What I'd change with a paid tier

Authenticated API (5,000 req/hour) so `triage` can check the timeline of every candidate rather than just the top ones. Still no write scope.
