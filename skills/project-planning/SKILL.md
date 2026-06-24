---
name: project-planning
description: Maintain a repo PLAN.md and concise progress checklist while coding. Use when the user asks to update plans, track remaining work, or report what changed and what is left after implementation.
---

# Project Planning

Use this skill to keep implementation work aligned with a living project plan.

## Core behavior

When working in a repo that has a `PLAN.md`, keep it current as implementation progresses.

After meaningful implementation work:

1. Update `PLAN.md` to reflect completed work.
2. Mark checklist items complete where appropriate.
3. Add newly discovered follow-up work if it materially affects the project.
4. In the final response, clearly state what changed and what remains.

If there is no `PLAN.md`, ask before creating one unless the user explicitly asks for planning/tracking.

## PLAN.md update rules

- Keep updates surgical.
- Do not rewrite the whole plan unless requested.
- Preserve the user's existing structure and terminology.
- Prefer checklist updates over prose rewrites.
- Add new tasks only when they are actionable and relevant.
- Do not mark speculative or partially implemented work complete.
- If no files changed, usually do not update `PLAN.md`; say no plan update was needed.

## Final response checklist

After implementation, include:

- Files or areas changed
- Validation commands run and whether they passed
- Whether `PLAN.md` was updated
- What remains to do

Keep the response concise.

Example:

```text
Implemented X.

Changed:
- path/to/file.ts
- path/to/other.tsx

Validated with:
- pnpm lint
- pnpm exec tsc --noEmit

Updated PLAN.md.

What’s left:
- Y
- Z
```

## Remaining-work lists

When listing remaining work:

- Prefer bullets.
- Keep items concrete.
- Order them roughly by implementation dependency.
- Do not include unrelated cleanup unless it was already part of the task or plan.

## Validation expectations

Run the smallest relevant validation for the change:

- TypeScript project: `tsc --noEmit` when practical
- Lintable repo: package manager lint script when available
- Tests: relevant test command if a test suite exists or was touched

Detect package manager before suggesting commands.

## Handling user preferences

If the user asks for a standing behavior such as “after every message, update the plan and tell me what is left,” follow it for the rest of the session.

For explanation-only responses where no repo files changed:

- Do not force a `PLAN.md` edit.
- Say `PLAN.md` was unchanged/no update needed.
- Still include what remains if the user asked for that behavior.
