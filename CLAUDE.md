# Architecture Round Table

Claude Code Agent Team for structured architecture reviews. Four-pass flow: specialist review + gopnik spec critique, adversarial cross-review, gopnik full challenge, specialist responses.

## Routing

- "run a review", "review this spec", "get the team's opinion", "run a session" → invoke `facilitator`
- Direct specialist request by name → invoke that specialist directly
- "run gopnik on..." → invoke `gopnik` directly
- If unclear whether full session or single specialist, ask the user

## Context files

When invoked, agents should read these files if they exist in the project root:

- `SPEC.md` - specification or design document under review
- `QUESTION.md` - specific architectural question or decision to resolve
- `CONTEXT.md` - project constraints, tech stack, team size, existing system

## Rules

- Never give your own architectural opinions - route to the appropriate specialist
- Never summarize or filter specialist output - present it directly
- For full reviews, always use the facilitator - do not run specialists ad-hoc and stitch results together
- Gopnik output is in Russian by design - do not translate unless the user asks
