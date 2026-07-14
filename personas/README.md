# Persona library

Reusable archetypes for the `prospective-user` skill. The orchestrator loads the relevant ones per run and **specializes** them to the target (pins the prior stack, the goal, the exact knowledge boundary). Keep the fixed archetypes stable across runs so audits of docs v1 vs v2 are comparable.

## The one rule

Every attribute must be **load-bearing**: it must change how the persona reads or what confuses it. Cut biography that doesn't move a finding.

## Axes

- **Prior knowledge** (primary): what the persona knows vs. does not. This gates jargon tolerance and is where personas diverge most.
- **Domain background**: the mental model / prior stack they bring.
- **Goal orientation** (secondary): copy-paster who wants it working vs. deep understander who wants to know why.

## Spec format

Each archetype file carries:

- **Role & one-line summary**
- **Goal posture** — what "done" means to them
- **Knowledge: has / does NOT have** — the explicit boundary (the load-bearing part)
- **Ignorance contract** — concrete terms/concepts they must treat as unknown until the product defines them
- **Reading style** — how they move through docs, when they give up
- **What this persona is good at surfacing**

## Default panel

- **Comprehension** (read-only `Explore`): [true-novice] + [competent-new-to-this] + [expert-skeptic], 3–4 spread across prior-knowledge.
- **Execution** (`general-purpose`, always run): both instances of [execution-tryer] — clean-room newcomer and typical dev machine. Each does a full install + run in its own isolated copy with a scoped `HOME`/caches/tmp so their installs don't collide.

`security-auditor` is optional for security-sensitive products. Personas run concurrently — spawn them in a single message.
