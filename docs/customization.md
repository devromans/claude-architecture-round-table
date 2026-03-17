# Customization

## Adding context to agents

Each agent reads files from the project directory. Create a `CONTEXT.md` with constraints relevant to your project:

```markdown
# Project Context

## Tech stack
- Language: Python 3.12
- Framework: FastAPI
- Database: PostgreSQL 16 + Qdrant
- Infrastructure: Docker Compose on a single VM

## Constraints
- Team size: 2 engineers
- No Kubernetes — single host only
- EU data residency required
- Must integrate with [existing system]

## Non-negotiables
- OAuth2 PKCE for all auth flows
- No third-party managed databases
```

Add a reference to `CONTEXT.md` in your `CLAUDE.md`:

```markdown
When running reviews, agents should read CONTEXT.md for project constraints.
```

## Adding a new specialist

Create a new file in `.claude/agents/`:

```markdown
---
name: your-specialist
description: >
  [One paragraph: what this agent does, when to invoke it directly,
  when to invoke via facilitator. This description is used by Claude
  to decide when to spawn this agent.]
---

# Role: [Name]

[Role definition]

## Your focus areas
[List]

## How you work
[Process]

## Output format
[Template]

## What you do not cover
[Explicit boundaries — what belongs to other specialists]
```

Then add the new agent to the facilitator's team table in `facilitator.md`.

Common additions:
- **Frontend Architect** — UI architecture, component design, state management, performance
- **Platform Engineer** — CI/CD, observability, on-call, SLOs, incident response
- **ML Architect** — model selection, inference infrastructure, data pipelines for ML, evaluation

## Adjusting the session format

The facilitator's output format is defined in `facilitator.md`. Edit the `Output format` section to match your preferred decision log structure.

If your team uses a specific ADR (Architecture Decision Record) format, add it as a template and instruct the facilitator to produce output in that format.

## Adapting the gopnik

The gopnik prompt is in Russian by design (see [`how-it-works.md`](how-it-works.md)). If you want to adapt it:

**To change the language**: replace all Russian text in `gopnik.md` with your target language. Keep the same structure — the sections are what matter.

**To make it harder**: add to the "Что ты ищешь" section. Common additions:
- Challenge whether the complexity of this design is justified for the team size
- Question whether existing solutions (open source, SaaS) were adequately considered
- Flag any decision that cannot be reversed without significant cost

**To make it softer** (not recommended — defeats the purpose): replace the tone with more diplomatic framing. Accept that you will get less signal.

## Running without gopnik

If you want a session without the devil's advocate passes (both Pass 1 spec critique and Pass 3 full challenge):

```
Run a full architecture review of SPEC.md. Skip gopnik.
```

The facilitator will run specialist review (Pass 1) and cross-review (Pass 2), then produce a decision log without the gopnik challenge rounds or Pass 4 specialist responses.

## Multi-session workflow

For large specifications, run sessions by domain:

```
Session 1: Run a data architecture review of SPEC.md
Session 2: Run a security review of SPEC.md, using the data architecture decisions from SESSION-1-OUTPUT.md as context
Session 3: Run gopnik on SESSION-1-OUTPUT.md and SESSION-2-OUTPUT.md combined
```

Paste previous session outputs into new context files and reference them explicitly.
