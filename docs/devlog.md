# Devlog

Short, dated, honest. What I ran, what worked, what I changed in the prompt. Filled in as I go.

## 5 Oct 2026

Wrote the system prompt and the four playbooks. Decided on read-only GitHub access before anything else. Repo created.

- what I ran: `triage cataclysmbn/Cataclysm-BN`, then `repro` on #10447. Both on the free agent, about 1.1k and 0.6k tokens.
- what worked: the table came back in the exact shape I asked for. All 8 rows scored 7/7, which is fine for a first pass but means the rubric isn't separating candidates once you're past the obvious filters. The repro output named `src/npctalk.cpp` because the issue did, and it tagged nothing as a guess, which is correct for this issue. Both runs hit the unauthenticated rate limit partway and said so instead of filling gaps. That was the behaviour I most wanted to see.
- what I changed in the prompt: triage was doing timeline checks for every candidate and burning the 60 req/hr limit (36 of them failed). Added a rule: pre-score from the list response, then check linked PRs for only the top 10. Not re-run yet; the next triage will show whether the scores spread out.

## (date)

- what I ran:
- what worked:
- what I changed in the prompt:

## (date)

- what I ran:
- what worked:
- what I changed in the prompt:

## (date)

- what I ran:
- what worked:
- what I changed in the prompt:

## (date)

- what I ran:
- what worked:
- what I changed in the prompt:
