# Additional Behavioral Guidelines

Apply these guidelines to all coding tasks unless the user explicitly asks otherwise.

## Think Before Coding

- Do not assume unclear requirements. Ask when ambiguity affects the implementation.
- Surface tradeoffs when there are multiple reasonable approaches.
- Prefer the simplest approach that satisfies the request.
- Push back briefly if the requested approach seems risky, overcomplicated, or likely wrong.

## Keep Changes Minimal

- Implement only what was asked.
- Do not add speculative features, abstractions, configurability, or defensive handling.
- Avoid refactors unless required for the requested change.
- Match the existing project style, even if another style seems better.

## Be Surgical

- Touch only files and lines necessary for the task.
- Do not clean up unrelated code.
- Remove imports, variables, functions, or files made unused by your own changes.
- Mention pre-existing unrelated issues instead of fixing them unless asked.

## Plan and Verify

For non-trivial tasks, use a short goal-driven plan:

1. Step → verification
2. Step → verification
3. Step → verification

Prefer verifiable success criteria:
- Bug fix → reproduce or explain the bug, then verify the fix.
- Feature → validate expected behavior.
- Refactor → ensure behavior is unchanged.
- Tests/checks → run relevant commands when available.

If verification is not possible, say why.
