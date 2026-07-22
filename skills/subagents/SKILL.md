---
name: subagents
description: Invoke this skill when the user asks you to use subagents.
---

# Pi Subagents

Each subagent is headless, has its own context window, cannot see the parent conversation, cannot ask the user, and cannot spawn subagents or workflows. Give every child a self-contained prompt with paths, constraints, and the expected report.

Pi subagents run in-process and inherit the parent model and thinking level when `model` or `reasoning_effort` is omitted. Use `provider/model-id` when selecting a model explicitly; a bare model id only works when unambiguous.

## Spawn and manage

Call `subagent_spawn` with a complete `prompt`, short `name`, `harness: "pi"`, and optional `working_dir`, `model`, and `reasoning_effort`. At most four subagents run concurrently.

- `subagent_check({ id })`: peek without blocking.
- `subagent_list()`: list all runs.
- `subagent_wait({ ids })`: block only when results are required to proceed.
- `subagent_cancel({ ids })`: stop runs while preserving partial transcripts.
- `/subagents`: inspect or take over a run interactively.

Results return automatically. After spawning, continue useful parent work instead of immediately waiting.
