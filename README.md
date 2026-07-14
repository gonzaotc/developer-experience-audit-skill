# developer-experience-audit-skill

A Claude skill that audits a technical product for **cognitive overhead** by simulating curious prospective users.

The orchestrator spawns persona sub-agents of varying skill levels, each reading the product **blank-slate** (artifact only, no web, no implementation source), attempting a real goal, and logging where their understanding breaks. Output is a ranked DX report: the exact terms, steps, and questions a newcomer could not resolve from the product alone.

Built on established UX/QA methods — cognitive walkthrough, think-aloud, Nielsen's heuristics and severity scale, Diátaxis, and information scent (see `references/methods.md`).

## Use

Invoke the `developer-experience-audit` skill with a target and, ideally, a concrete goal:

```
/developer-experience-audit <target path> "reduce gas on my ERC20"
```

- **target** — the product to audit (repo, docs dir, SDK, CLI, or another skill).
- **goal** (optional) — a concrete task; sharper than free exploration.
- **sample project** (optional) — lets the execution persona actually run the product.

The report lands in `<target>/dx-audit/<date>.md` plus a terminal summary.

## Layout

- `SKILL.md` — the orchestrator: flow, interaction protocol, finding schema, aggregation.
- `personas/` — reusable archetypes spread across the prior-knowledge axis.
- `templates/report.md` — the audit report shape.
- `references/methods.md` — the methods the skill operationalizes.

## Known limit

The model is itself an expert, so it under-reports the friction a real beginner feels. This reliably catches undefined concepts, broken examples, missing prerequisites, and mode mismatches; treat findings as leads to confirm with real users, not a replacement for user testing.
