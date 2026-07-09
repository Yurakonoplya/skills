# Fable Ruki-Agenty (Fable Hands-Agents)

A manually-invoked orchestration mode for Claude Code: the orchestrator is the head and nothing else. It thinks, resolves forks and writes complete, self-sufficient specs into GitHub issue bodies; all mechanics — coding, review, gh operations, reading, scouting, DoD verification — go to subagents on a cheaper model (Opus; never lower).

The pipeline operates on roles (scout, implementer, gh-agent, verifier, reviewer) and three operations: dispatch (an Agent tool call), resume the thread (SendMessage to the same agent), fresh context (a new agent). An optional external channel — Codex — exists primarily for cross-model-family final review; its mechanics live in [`references/channels.md`](references/channels.md). The channel configuration is confirmed with the user at the start of every run.

## Core rules

- **The split gate.** Work may not be divided between agents while one part's execution can still change another part's brief. Three markers mean "too early to split": the brief branches on facts not yet discovered ("if it turns out that…"), the agents would interrupt each other if seated in one room, or their reports would have to be reconciled rather than simply added up. The first two are only cured by phasing — one head investigates; the third is cured by freezing the shared decisions into a contract first.
- **Saturation loop for research and diagnosis.** When the list of things to investigate is itself the subject of the investigation (market/competitor research, debugging, incident analysis), a single scout works one long context until the map stops growing — a mechanical stop rule: five searches in a row yielding no new entity. Only the resulting closed list is fanned out to parallel scouts, each carrying the full map and required to report "new entities not on the map". Then integrate, and loop if the map grew.
- **Issue-first, no exceptions.** Every task becomes a GitHub issue whose body is a full spec (goal, context, resolved forks, steps, boundaries, DoD) before anything is dispatched.
- **Dispatch by pointer.** The implementer reads the issue body via `gh issue view N`; the prompt is just an operational envelope.
- **Fresh-context verifier per task.** A separate subagent runs the DoD check; the one who built it never accepts it. An "unverifiable" verdict is legal and does not close the issue.
- **Escalation ladder on failure:** two reworks by the same implementer → fresh implementer with the verifier's diagnosis → `blocked` label and a short report to the user.
- **Async spec-ahead.** While an implementer works, the orchestrator writes the next specs instead of waiting.
- Parallelism is decided by the split gate first and file overlap second, never by agent count: same-file tasks run sequentially with direct commits to main; file-disjoint groups whose briefs are already frozen may run in parallel worktrees.

## Why

The orchestrator has two scarce resources. Its model's tokens: the strongest model has the smallest limits, a subagent on Opus is several times cheaper, and Codex burns a different subscription altogether. And its context: a clean context means a long session without compactions and more work done within it. This skill turns the orchestrator into a pure spec-writer and dispatcher — but judgment is never delegated, and neither is a phase whose questions are still open.

This skill is invoked manually (`disable-model-invocation: true`).

Русская версия: [README.ru.md](README.ru.md)

Source: [serejaris/personal-corp-skills](https://github.com/serejaris/personal-corp-skills/tree/main/skills/fable-ruki-agenty) (MIT). The split gate and the saturation loop are added here — see [issue #11](https://github.com/serejaris/personal-corp-skills/issues/11).
