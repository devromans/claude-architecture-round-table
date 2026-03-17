# Architecture Round Table

> A Claude Code Agent Team boilerplate for structured architecture reviews.

A team of specialized agents reviews your specification or design - each from a distinct perspective - then a devil's advocate challenges everything the team agreed on.

Inspired by the practice of gathering senior engineers in a room with a whiteboard and not leaving until decisions are made.

> **Note:** This project uses Claude Code's [Agent Teams](https://docs.anthropic.com/en/docs/claude-code) feature (experimental). Agents are spawned as teammates coordinated by a facilitator. See [Requirements](#requirements) and [Setup](#setup) below.

## What it does

You provide a specification, design document, or architectural question. The agents produce:

- Domain-specific findings (Critical / High / Medium / Low)
- Explicit conflicts between specialists
- A structured decision log
- Devil's advocate passes that challenge the spec itself, severity classifications, assumptions, and consensus

## Agents

| Agent | Domain |
|---|---|
| **Facilitator** | Orchestrates the session. No opinions - only coordination and synthesis. Runs four passes: specialist review + gopnik spec critique, cross-review, gopnik full challenge, specialist responses. |
| **Software Architect** | System design, APIs, infrastructure, scalability, reliability |
| **Data Architect** | Schema design, storage strategy, query patterns, migrations, consistency |
| **Security Architect** | Threat model, auth, access control, secrets, data exposure |
| **Product Architect** | One question only: does this design solve the actual user problem, and is the scope right? |
| **Operations Architect** | Two questions: can this system fail silently, and can it be rolled back safely? Also covers deployment risk. |
| **Cost Architect** | Wrong tool for the job, cost traps, hidden costs, economic red flags at scale |
| **Gopnik** | Devil's advocate. Invoked twice: Pass 1 critiques the spec before specialists report; Pass 3 challenges the spec, all findings, and consensus. |

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed (v2.1.32+)
- Claude Pro, Max, Team, or Enterprise plan
- Agent Teams feature enabled (experimental, see Setup)

## Setup

```bash
git clone https://github.com/devromans/claude-architecture-round-table
cd claude-architecture-round-table
```

Agent Teams are experimental and disabled by default. This repo includes `.claude/settings.json` that enables them automatically via `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. If you need to enable manually:

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

Running in tmux is recommended - each teammate gets its own pane, making it easy to follow the session. Claude Code's native iTerm2 split-pane integration (without tmux) has known bugs; use tmux instead:

```bash
tmux -CC new-session -s architecture-round-table
```

Alternatively, Agent Teams support in-process mode (all teammates in a single terminal, cycle with `Shift+Down`):

```bash
claude --teammate-mode in-process
```

Add your specification:

```bash
cp your-spec.md SPEC.md
```

Start a session:

```bash
claude
```

## Usage examples

**Full review:**
```
Run a full architecture review of SPEC.md
```

**Focused review:**
```
Use the security architect to review the auth flow in SPEC.md
```

**Single decision:**
```
The team is deciding between PostgreSQL and MongoDB for this use case. Run a session to resolve it.
```

**Challenge existing output:**
```
Run gopnik on this session output: [paste previous output]
```

## Customization

See [`docs/customization.md`](docs/customization.md) for:
- Adding domain-specific context (your tech stack, constraints, team size)
- Creating new specialist agents
- Adjusting the session format

## How it works

The facilitator reads your input and runs four passes:

**Pass 1 - Specialist review + gopnik spec critique** (parallel): Each specialist reviews the spec from their domain. Independent specialists run in parallel; specialists with dependencies (security reads software's auth design; cost reads ops's deployment model) run sequentially. Simultaneously, gopnik reads only the spec and critiques it before seeing any team output.

**Pass 2 - Adversarial cross-review**: Specialists see each other's Pass 1 findings and gopnik's spec critique. They add cross-domain observations and flag conflicts. A security finding may have cost implications the cost architect didn't model. A product scope cut may remove the use case that justified an architectural decision.

**Pass 3 - Gopnik full challenge**: Gopnik reads the original spec, all Pass 1 specialist outputs, and all Pass 2 cross-review outputs. Challenges the spec itself, escalates severity, and surfaces what everyone agreed on without sufficient justification.

**Pass 4 - Specialist responses to gopnik**: Specialists that gopnik challenged with new findings in Pass 3 respond directly - accept, reject, or escalate. Only affected specialists are invoked.

See [`docs/how-it-works.md`](docs/how-it-works.md) for the full design.

## The gopnik

Gopnik is the devil's advocate agent. Its prompt is written in Russian — this is intentional. The language activates different associative patterns: skepticism, directness, distrust of authority, and a specific kind of bluntness that English "devil's advocate" prompts tend to lose. The output is in Russian; use your preferred translation tool if needed, or switch the language in the prompt.

See [`docs/customization.md`](docs/customization.md) if you want to adapt it.

## License

MIT
