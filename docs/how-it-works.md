# How it works

> **Prerequisite:** This project uses Claude Code's Agent Teams feature (experimental). See the [README](../README.md) for setup instructions.

## Design principles

**Specialization over generalism.** Each agent has a narrow domain and explicit boundaries. When a software architect encounters a schema question, they flag it for the data architect - not answer it themselves. This prevents the single-agent problem where one "smart" agent tries to cover everything and produces shallow analysis across the board.

**Coordination without opinion.** The facilitator's job is to frame questions precisely, collect outputs, surface conflicts, and produce a decision log. It never gives its own architectural opinion. This keeps it from contaminating the specialists' independent analysis.

**Parallel by default, sequential where dependent.** Specialists with independent concerns run in parallel. Security architect reads the software architect's auth design before reviewing it. Product architect's scope decision affects what the data architect needs to model. These dependencies are explicit in the facilitator's routing rules.

**Gopnik twice, not once.** The devil's advocate is invoked in Pass 1 (reading only the spec, in parallel with specialists) and again in Pass 3 (reading everything). Pass 1 catches problems in the spec itself before the team has started. Pass 3 challenges the team's output, consensus, and severity classifications. If gopnik only ran last, it could only challenge what the team noticed - not what the spec got wrong.

**Specialists get the last word.** After gopnik's full challenge in Pass 3, affected specialists respond directly in Pass 4 - accept, reject, or escalate. This prevents gopnik from having unchallenged authority and forces the team to confront uncomfortable findings explicitly.

**Explicit conflicts, not smooth consensus.** When two specialists disagree, the facilitator surfaces the conflict as `[CONFLICT]` and assigns it as an open question. It does not resolve the conflict itself. Unresolved conflicts are more valuable than false consensus.

## Four-pass flow

```
User provides SPEC.md / QUESTION.md / CONTEXT.md
                       │
                       ▼
                 Facilitator
                       │
 ══════════════════════╪════════════════════════════ Pass 1
    │                  │                       │
    ▼                  ▼                       ▼
 Parallel          Sequential              Gopnik
    │                  │                (spec critique
    ▼                  ▼                 only, parallel
 Software          Operations           with specialists)
 Product           (after Software)          │
 Data                  │                     │
    │                  ▼                     │
    │              Security                  │
    │              (after Software)          │
    │                  │                     │
    │                  ▼                     │
    │              Cost                      │
    │              (after Ops + Software)    │
    ▼                  ▼                     ▼
 ══════════════════════╪════════════════════════════ Pass 2
                       │
                 All specialists
                 see each other's
                 Pass 1 outputs +
                 gopnik spec critique
                       │
                 Cross-review
                 (adversarial)
                       │
 ══════════════════════╪════════════════════════════ Pass 3
                       │
                    Gopnik
                 reads everything:
                 spec + Pass 1 +
                 Pass 2 outputs
                       │
                 Full challenge
                       │
 ══════════════════════╪════════════════════════════ Pass 4
                       │
                 Affected specialists
                 respond to gopnik's
                 new challenges
                       │
                 Accept / Reject /
                 Escalate
                       │
                       ▼
                Decision Log
```

## Severity model

All specialists use the same four-tier severity model:

| Severity | Meaning |
|---|---|
| **CRITICAL** | Will cause production failures, data loss, security breaches, or fundamental user harm |
| **HIGH** | Significant risk or operational pain at scale; requires design change |
| **MEDIUM** | Sub-optimal but workable; accumulates technical debt |
| **LOW** | Stylistic or minor; worth noting but does not block |

Gopnik may escalate severity from any specialist when it believes the classification was too conservative.

## Decision log format

Every session ends with a structured log:

```
**Decisions:**
- [DECIDED] ...

**Conflicts:**
- [CONFLICT] X vs Y on [topic]: [description]

**Open questions (owner):**
- [question] → owner: [agent name]

**Deferred:**
- [topic] → reason: [why deferred]
```

Open questions without an owner are not allowed. Every unresolved question must have an agent responsible for answering it in the next session.

## Why the gopnik prompt is in Russian

Language is not decoration - it activates different associative patterns in the model. The Russian prompt for the devil's advocate agent produces a distinct tone: direct, skeptical, low diplomatic overhead. English "devil's advocate" prompts tend to produce structured pushback with polite framing. The Russian version is blunter by construction. This is a deliberate architectural decision, not a localization artifact.

If your team does not read Russian, use a translation tool on the output, or adapt the prompt to your preferred language while preserving the directness - see [`customization.md`](customization.md).
