# Prospective-User DX Audit: {{target}}

| | |
|---|---|
| Target | {{target path or name}} |
| Goal | {{concrete goal, or "free exploration"}} |
| Date | {{YYYY-MM-DD}} |
| Model | {{model that ran the audit, e.g. claude-opus-4-8}} |
| Panel | {{personas used}} |
| Execution | {{clean-room + typical-dev-machine — against sample project @ path | against empty dir per getting-started}} |

## Headline: Time to Hello World

- **Reached a working result?** {{yes / no / partial — which personas}}
- **Steps on the getting-started path:** {{n}} · **stalls:** {{n}} · **longest stall:** {{where}}
- **One-line verdict:** {{e.g. "A domain expert gets there; a newcomer stalls at step 3 on undefined setup."}}

## Ranked findings

Sorted by severity (Nielsen 0–4) × coverage × skill-breadth. Cut line for "fix before shipping" is severity ≥ 3.

| ID | Sev | Cov | What | Where | Heuristic | Personas |
|----|-----|-----|------|-------|-----------|----------|
| PU-01 | 4 | 3/3 | | | | |

<a id="pu-01"></a>
### PU-01 · {{what}} — severity {{0-4}}, coverage {{n}}/{{panel}}

**Where.** `{{file:line | section | command}}`

**Blocking question.** {{the exact question no persona could answer from the product}}

**Expectation vs. reality.** {{what the reader expected vs. what the product gave}}

**Heuristic / mode.** {{Nielsen heuristic violated; Diátaxis mode-mismatch if any}}

**Who hit it.** {{personas}} — {{note any disagreement, e.g. "expert resolved via convention; novice hard-stopped"}}

**Evidence.** {{quote / line ref / command output}}

**Suggested fix.** {{concrete doc/API change that would remove the friction}}

<!-- repeat per finding -->

## Focus areas

One subsection per focus direction the user requested (conceptual clarity, findability, contribution path, rationale, agent discoverability, …). For each: the verdict and the findings that bear on it. Omit if no focus directions were given.

### Agent discoverability (if requested)

Per plausible user intent, whether an agent would fire the right tool from the trigger surface alone.

| User intent (what they'd say) | Right tool | Would an agent route there? | Gap |
|---|---|---|---|
| "I want to add a gas technique" | solidity-gas-reference-creator | | |

## Questions the product left unanswered

First-class list of questions personas asked and could not resolve from the product. Each is a documentation gap with a name.

- {{question}} — asked by {{persona}} at {{where}}
- ...

## Getting-started friction

Narrative of the onboarding path: each atomic step, whether it passed the four cognitive-walkthrough questions, and where the path broke. Setup friction is called out because it is disproportionately costly.

## Runtime experience (execution personas)

What actually happened when the product was installed and run. Numbers required.

**Real Time-to-Hello-World:** {{clean-room: Xs · typical-dev-machine: Ys}}

| Step | Command | Duration | Output? | Notes |
|------|---------|----------|---------|-------|
| | | {{Ns}} | {{yes/no}} | {{friction, retries, error}} |

- **Slowest steps.** {{command → duration}}
- **Silent steps (no textual feedback).** {{command ran Ns with zero output — user can't tell it's working}}
- **Errors hit.** {{command → message; was the message actionable?}}
- **Workarounds the docs didn't give.** {{what the typical-dev-machine persona had to invent}}
- **Clean-room vs warm-machine divergence.** {{what only worked because something was already installed}}

## Assumed-knowledge disagreements

Where a novice was blocked but a more expert persona was not — these mark knowledge the product silently assumes.

- {{locus}}: assumes {{X}}. Fine for {{persona}}, wall for {{persona}}.

## Per-persona journeys (appendix)

### {{persona}}
{{5–10 line narrative: where they flowed, where they stalled, where they gave up}}

## Method & limitations

Findings from simulated personas reading blank-slate ({{n}} personas, web denied, artifact-only). This catches undefined concepts, broken examples, missing prerequisites, and mode mismatches well; it only approximates real newcomer frustration and under-reports emotional give-up points. Treat as leads to confirm with real users, not a replacement for user testing.
