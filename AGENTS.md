# Model Selection

- When spawning a subagent, default to `openai-codex/gpt-5.4-mini` with `reasoning_effort: "medium"`.
- Use `openai-codex/gpt-5.6-terra` only when a subtask needs complex implementation or deep reasoning.
- Do not use bare model IDs or `openai/...` models: this setup authenticates through the `openai-codex` OAuth provider.
- The subagent extension enforces the lightweight default when a model is omitted; explicitly select the parent model when inheritance is needed.
