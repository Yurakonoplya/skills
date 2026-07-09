# Fable Ruki-Agenty (Fable Hands-Agents)

A manually-invoked orchestration mode for Claude Fable 5 under tight usage limits: Fable is the head and nothing else. It thinks, resolves forks and writes complete, self-sufficient specs into GitHub issue bodies; all mechanics — coding, review, gh operations, reading, scouting, DoD verification — go to cheap hands.

A hand is a pair of harness and model, and the pipeline does not depend on it: it knows roles (scout, implementer, gh-hand, verifier, reviewer) and four operations only (dispatch, resume the thread, fresh context, report). Every binding to concrete tools and models lives in a single file — [`references/channels.md`](references/channels.md); by default the hands are Claude Code subagents, and swapping the environment or a hand means editing that file, not the pipeline.

## Core rules

- **The split gate.** Work may not be divided between agents while one part's execution can still change another part's brief. Three markers mean "too early to split": the brief branches on facts not yet discovered ("if it turns out that…"), the agents would interrupt each other if seated in one room, or their reports would have to be reconciled rather than simply added up. The first two are only cured by phasing — one head investigates; the third is cured by freezing the shared decisions into a contract first.
- **Saturation loop for research and diagnosis.** When the list of things to investigate is itself the subject of the investigation (market/competitor research, debugging, incident analysis), a single scout works one long context until the map stops growing — a mechanical stop rule: five searches in a row yielding no new entity. Only the resulting closed list is fanned out to parallel scouts, each carrying the full map and required to report "new entities not on the map". Then integrate, and loop if the map grew.
- **Issue-first, no exceptions.** Every task becomes a GitHub issue whose body is a full spec (goal, context, resolved forks, steps, boundaries, DoD) before anything is dispatched.
- **Dispatch by pointer.** The implementer reads the issue body via `gh issue view N`; the prompt is just an operational envelope.
- **Fresh-context verifier per task.** A separate hand runs the DoD check; the one who built it never accepts it. An "unverifiable" verdict is legal and does not close the issue.
- **Escalation ladder on failure:** two reworks by the same implementer → fresh implementer with the verifier's diagnosis → `blocked` label and a short report to the user.
- **Async spec-ahead.** While an implementer works, Fable writes the next specs instead of waiting.
- Parallelism is decided by the split gate first and file overlap second, never by agent count: same-file tasks run sequentially with direct commits to main; file-disjoint groups whose briefs are already frozen may run in parallel worktrees.

## Why

Fable tokens are the most expensive resource in the session. Every tool call a cheaper model could have made is burned quota. This skill turns Fable into a pure spec-writer and dispatcher — but judgment is never delegated, and neither is a phase whose questions are still open.

This skill is invoked manually (`disable-model-invocation: true`).

Русская версия: [README.ru.md](README.ru.md)

Source: [serejaris/personal-corp-skills](https://github.com/serejaris/personal-corp-skills/tree/main/skills/fable-ruki-agenty) (MIT). The split gate and the saturation loop are added here — see [issue #11](https://github.com/serejaris/personal-corp-skills/issues/11).
