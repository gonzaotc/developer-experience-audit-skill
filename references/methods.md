# Methods reference

The established UX/QA methods this skill operationalizes. Sources are primary (Nielsen Norman Group, Diátaxis, HCI literature).

## Cognitive walkthrough — the spine

A task-based inspection for **learnability** (first use). Decompose the task into atomic steps; at each step answer four questions. Any "No" is a localized failure.

1. Will the user try the action that achieves their goal? (Do they know this is the step?)
2. Will the user notice the correct action is available/findable?
3. Will the user associate that action with the result they want? (Does the label map to intent?)
4. After acting, will feedback confirm progress?

→ Personas run this over the getting-started path; a "No" logs a finding with a step index.
Source: https://www.nngroup.com/articles/cognitive-walkthroughs/

## Think-aloud with prediction

The user verbalizes thoughts continuously while working. The highest-signal event is a **prediction that the product then contradicts**. ~5 users surface most problems.

→ Personas predict a command's/section's outcome *before* reading it and state confidence; the expectation-vs-reality gap is logged.
Source: https://uxpajournal.org/intervene-think-aloud-protocols-usability-testing/

## Personas

A good persona captures a useful mental model of a user: **goals, skill level / prior knowledge, mental model**. Every attribute must be load-bearing. Vary personas primarily on **prior knowledge** — divergence between novice and expert at the same locus reveals *who* a gap hurts.
Source: https://www.nngroup.com/articles/persona/

## Nielsen's 10 heuristics (tag each finding)

Bold = strongly applies to docs/DX.

1. **Visibility of system status** — docs show the expected output of a step.
2. **Match between system and the real world** — familiar language, not internal jargon.
3. User control and freedom.
4. **Consistency and standards** — same term = same concept throughout.
5. **Error prevention** — warn about prerequisites and common misconfig up front.
6. **Recognition rather than recall** — don't force the reader to hold earlier context; restate/link.
7. Flexibility and efficiency — quickstart for novices, shortcuts for experts.
8. **Aesthetic and minimalist design** — no wall of text burying the essential path.
9. **Help users recognize/diagnose/recover from errors** — plain-language troubleshooting for real errors.
10. **Help and documentation** — searchable, task-focused, in-context.

Source: https://www.nngroup.com/articles/ten-usability-heuristics/

## Diátaxis — documentation mode mismatch

Four modes, each for a different reader posture: **tutorial** (learning), **how-to** (a specific task), **reference** (facts), **explanation** (understanding). Friction arises when content's mode fights the reader's current goal — e.g. a quickstart that demands reference-level config knowledge, or a tutorial that explains architecture before the user has run anything.

→ Give the persona a mode intent ("I just want hello-world" = tutorial posture); flag every moment the doc forces a different mode.
Source: https://diataxis.fr/

## Curse of knowledge / expert blind spot

Once mastered, a skill automates and the expert can no longer model not-knowing it, so they omit steps and prerequisites that feel "obvious". A blank-slate reader hits exactly those omissions. This is the skill's reason to exist — and its central pitfall, since the LLM is itself an expert (see limitations).
Source: https://en.wikipedia.org/wiki/Curse_of_knowledge

## First-click / information scent

A correct first navigation choice strongly predicts task success. **Information scent** is the reader's estimate, from labels and context, of whether a path leads to their goal; they abandon when scent goes weak.

→ From a landing page or nav, ask the persona which link it would follow for its goal *and why*, before reading on. Wrong/uncertain choice = weak scent (vague headings, missing entry point).
Source: https://www.nngroup.com/articles/information-scent/

## Severity 0–4

Rate each finding by frequency × impact × persistence.

- 0 — not a usability problem
- 1 — cosmetic
- 2 — minor
- 3 — major (fix before shipping)
- 4 — catastrophe (must fix)

Cross-persona coverage is a natural proxy for frequency.
Source: https://www.nngroup.com/articles/how-to-rate-the-severity-of-usability-problems/

## Documentation-specific detectors

- **Undefined jargon** — term used before defined/linked. Treat as a structural check, not a judgment the model can rationalize away.
- **Unexplained prerequisite** — a step needing a tool/account/key/concept not previously established.
- **Time-to-Hello-World** — steps, context-switches, and stalls before first working output. The headline DX metric.

Sources: https://getdx.com/blog/developer-documentation/ · https://buildwithfern.com/post/developer-documentation-metrics

## Pitfalls (baked-in mitigations)

- **Expert-knowledge leakage** — the LLM fills gaps a novice couldn't. Mitigate: hard knowledge-boundary prompts, artifact-only rule, flag-don't-resolve, treat jargon detection structurally.
- **Over-competence / no real frustration** — mitigate with budget-based abandonment rules.
- **Confabulated success** — require predicted-output; mark unverifiable steps "assumed".
- **Small-N variance** — run a multi-persona panel and aggregate, don't trust one pass.
- **Biography drift** — keep every persona attribute load-bearing.
