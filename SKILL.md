---
name: developer-experience-audit
description: Audit a technical product for cognitive overhead by simulating curious prospective users. Use when asked to evaluate developer experience, run a DX or docs audit, test learnability, find where docs are confusing, simulate how a newcomer reads a product, or check onboarding friction for a Solidity library, SDK, CLI, skill, or documentation set.
allowed-tools: Read, Glob, Grep, Bash, Write, Agent
argument-hint: "[target path or docs dir] [optional: a concrete goal, e.g. \"reduce gas on my ERC20\"]"
---

# Prospective User

Spawn persona sub-agents that read a product blank-slate, attempt a real goal, and log where their understanding breaks. The output is a ranked list of cognitive-overhead findings with evidence: the exact terms, steps, and questions a newcomer could not resolve from the product alone.

The premise is the author's **curse of knowledge**: once you know a product, you can no longer see what a newcomer doesn't. A blank-slate reader hits exactly the steps the author silently assumed. That gap is the deliverable.

## Non-negotiables

1. **Personas read the artifact only.** Web search/fetch is forbidden to personas. Reaching outside the product for an answer is itself a finding, not a resolution.
2. **No implementation source.** Personas read only what a real user sees: README, docs, public signatures, `--help`, CLI/skill instructions, examples, scripts-as-text. They must not open internal implementation to resolve confusion a user couldn't.
3. **Blank slate is enforced, not assumed.** A persona reasons from the artifact text it has read so far, not from world knowledge. When tempted to auto-resolve a term from its own knowledge, it must **flag** it instead of resolving it.
4. **Every finding has a locus.** `where` (file+line, section, or command+output) and the `blocking-question` are mandatory. A finding without them is noise; drop it.
5. **Never edit the target.** Comprehension personas are strictly read-only. The two execution personas each work only in their own isolated copy with a scoped environment — never the target, never each other's copy.
6. **Report what happened.** If a persona hard-stopped, say so. Mark unverifiable steps "assumed", never "passed". Negative results (couldn't even start) are findings.

## Inputs

- **target** (required): path to the product, or a docs directory/URL-less local surface.
- **goal** (optional): a concrete task ("integrate a capped ERC20", "reduce gas on my contract"). With a goal → task-driven audit. Without → free exploration. A concrete goal produces sharper findings; prefer one.
- **focus directions** (optional, asked for): aspects the user wants emphasized (see step 0).
- **sample project** (optional): a throwaway codebase for the two execution personas to run the product against. Execution runs either way — without one, they start from an empty dir and follow getting-started literally (and a quickstart that assumes a pre-existing project becomes a missing-prerequisite finding).

## Procedure

### 0. Ask for focus directions

Before anything else, ask the user whether there are aspects or directions to focus on for this run. Offer concrete examples so the question is easy to answer:

- **Conceptual clarity** — is it clear what each concept means and why?
- **Findability** — can a user locate the thing they need (where to look, information scent)?
- **Contribution path** — is "how to contribute" clear and correct?
- **Rationale** — is "why this exists / when to use it" answered?
- **Agent discoverability** — would an AI agent, given a plausible user intent, surface the right skill/tool? E.g. a user says "I want to add a technique" — does the agent recognize a skill exists for that and route to it, based solely on the skill's `description`?

Focus directions are additive, not exclusive: personas still run the full protocol, but the orchestrator biases persona selection, weights matching findings higher in the ranking, and adds a dedicated report section per requested focus. If the user has no directions, proceed with the general audit.

For an **agent-discoverability** focus specifically, add a probe: for each plausible user intent the user names (or that you infer), judge from the product's trigger surface alone (skill `description`, README, `argument-hint`) whether an agent would fire the right tool. A near-miss (the description omits the phrasing a user would use) is a high-value finding.

### 1. Scope the target

Read the entry surface yourself (README first, then what it links to locally) to map the reading path and identify the getting-started flow. Note the product type (Solidity library / SDK / CLI / skill / docs) — it only changes the execution step, not the loop.

### 2. Sanity-check the environment

Confirm base runtimes exist (e.g. `node`, `forge`, whatever the product needs) so genuine machine noise doesn't pollute findings. **Do not** run the product's own install/setup yourself — that path is part of the test, owned by the execution personas.

Then **provision isolation for the two execution personas before fan-out**. Two concurrent full-installs collide on shared global state — npm/foundry caches, global installs, lockfiles, ports — and a collision gets mislogged as a DX finding, which is exactly the noise this step exists to prevent. Worktree/copy isolation covers the working tree, not this. For each execution persona create an isolated copy **and** a scoped environment: its own `HOME`, cache dirs, and tmp (e.g. `HOME=<copy>/home`, `npm_config_cache`, `FOUNDRY_HOME`, `TMPDIR` all under the copy). Give the clean-room persona empty caches; the warm-machine persona may inherit real caches. Scoped env does not isolate fixed ports, so if the product's quickstart binds a fixed port (or scoping is otherwise impractical for a toolchain), stagger those two runs instead of running them truly concurrently, and note it.

### 3. Assemble the panel

Load archetypes from `personas/` and specialize each to this target (e.g. "novice → novice whose only prior stack is Node/JS, never touched a wallet"). Default panel:

- **3–4 comprehension personas** spread across the prior-knowledge axis — one true novice, one competent-but-new-to-this-product, one expert/skeptic — run as read-only `Explore` sub-agents.
- **2 execution personas** (from `personas/execution-tryer.md`): the **clean-room newcomer** and the **typical dev machine**. Both do a full install + end-to-end run; they diverge in environment and posture so runtime friction gets two independent opinions. Run as `general-purpose` sub-agents (they need Bash to run and Write to follow "create a project" steps).

Both execution personas run every time, not only when a sample project is supplied. They run against the sample project if one was given; otherwise they start from an empty dir and do exactly what getting-started says. If the quickstart assumes a project already exists, that is a missing-prerequisite finding — they must not paper over it by silently scaffolding.

Every persona attribute must be load-bearing: it must change how the persona reads or what confuses it. Cut decorative biography. Scale up only on request.

### 4. Fan out (parallel)

Spawn **all personas in a single message** (multiple Agent tool calls in one turn) so they run concurrently — this is the primary speed win. Comprehension personas use the `Explore` type; the two execution personas use `general-purpose` and each gets its isolated copy + scoped environment from step 2. Inject into each persona prompt:

- its specialized persona spec (from `personas/`),
- the target path, the goal, and any **focus directions** (so the persona pays extra attention there while still running the full protocol),
- for execution personas: the path to their isolated copy and the exact env vars scoping their `HOME`/caches/tmp,
- the **Interaction protocol** below verbatim,
- the **Finding schema** below, and the instruction to return findings as a JSON array plus a short journey narrative.

Forbidden-tools block for every persona prompt:

> Comprehension personas may use only Read, Glob, Grep. Execution personas may additionally use Bash and Write, scoped strictly to your isolated copy. **No persona may use WebSearch or WebFetch.** If answering a question would require the web or reading implementation source, that is a FINDING — log it and treat the question as unresolved.

### 5. Aggregate

Collect all persona findings. Then:

- **Dedup by locus.** Same `where` from N personas → one finding with `coverage: N`.
- **Assign severity (Nielsen 0–4)** yourself from frequency × impact × persistence — do not trust per-persona self-rating. Coverage feeds frequency. 4 = blocks everyone and persists; 0 = not a real problem.
- **Tag each finding** with the violated heuristic (see `references/methods.md`) and, for docs, the Diátaxis mode-mismatch when the content's mode fights the reader's goal.
- **Keep disagreements.** Novice blocked where the expert breezes through = a signal about assumed knowledge; report it explicitly, don't average it away. Same for the two execution personas: a step that worked warm but failed clean-room marks a silently-assumed prerequisite.
- **Rank runtime findings on their numbers.** A silent or slow step's severity scales with its measured `duration_s` and `had_output`; keep the number in the finding so the rank is falsifiable.

### 6. Write the report

Fill `templates/report.md` and write to `<target>/dx-audit/dx-audit-<target-slug>-<YYYY-MM-DD>-<model>.md` (unless the user named another location), where `<model>` is the model that ran the audit (e.g. `claude-opus-4-8`, `claude-sonnet`). Record that same model in the report's header table. Print a terminal summary: the headline metric, the top findings, and the unanswered-questions count.

## Interaction protocol (inject into each persona)

You are simulating a real prospective user. Finding friction is success; appearing competent is failure. Work from a **blank knowledge ledger**.

1. **Follow the real reading path.** Start at the entry doc (README). Follow only local/relative links. An external link you'd have to open to understand something is a finding.
2. **Keep a term ledger.** Every term, concept, or component the product introduces goes on the ledger on first use. Check it off only when the product defines it — inline, by context, or via a local link. Any entry still unresolved at the end is a finding.
3. **Think aloud with prediction.** Before you read what a command/section does, *predict* it and state your confidence. When the product contradicts or fails to confirm your prediction, log the gap — this is your highest-value output.
4. **Walk the getting-started path step by step.** Decompose it into atomic steps. At each step answer the four questions; any "No" is a finding tagged with the step index:
   - Will I try the right action to reach my goal? (Do I even know this is the step?)
   - Is the correct action findable? (Is it visible / documented where I'd look?)
   - Does the action's label/cue map to my intent?
   - After acting, does the feedback confirm I made progress?
5. **Resolve only from the artifact.** When stuck, search the readable surface. If you can't resolve it there, it stays unresolved — log the blocking question and either recover (note how, from where) or hard-stop.
6. **Abandon on budget, not vibes.** If a single step accumulates ≥3 unresolved unknowns, log abandonment at that step rather than pushing through. (Execution personas: follow your instance's posture — clean-room abandons on silence, typical-dev-machine pushes through and logs each workaround.)
7. Never invent success. If you can't actually run something, mark the step "assumed".
8. **Execution personas — measure the runtime.** Wrap every command in timing (e.g. `time <cmd>` or record start/end wall-clock) and note whether it produced any stdout/stderr. A slowness or silent-step finding must carry the number: "ran Ns with zero output". Sum step timings into the real Time-to-Hello-World.

Return: a JSON array of findings (schema below) + a 5–10 line journey narrative of where you flowed and where you stalled.

## Finding schema

```json
{
  "what": "one line: the friction",
  "where": "file:line | doc section | command + its output  (MANDATORY)",
  "expectation_vs_reality": "what I expected vs what happened",
  "blocking_question": "the exact question I could not answer from the product  (MANDATORY)",
  "resolution": "recovered (how/from where) | hard-stop",
  "persona": "which persona",
  "task_step": "step index in the getting-started flow, if applicable",
  "runtime": {
    "command": "the exact command run, if this is an execution finding",
    "duration_s": "wall-clock seconds for the step",
    "had_output": "true | false — did the command emit any stdout/stderr?",
    "retries": "how many times the persona had to retry / invent a workaround"
  }
}
```

The `runtime` object is present only on execution-persona findings (slowness, silence, errors, friction); omit it otherwise.

The orchestrator adds `coverage`, `severity` (0–4), `heuristic`, and `diataxis_mismatch` during aggregation.

## Limitations (state these in every report)

Expert-knowledge leakage is fundamental: the model cannot fully un-know things, so it under-reports the friction a real beginner would feel emotionally and over-resolves some jargon. This skill reliably catches **undefined concepts, broken/uncompilable examples, missing prerequisites, mode mismatches, dead-end questions, and now real runtime friction — install slowness, silent steps, errors, and workarounds — with measured timings** from two execution personas. It only approximates "a real newcomer would give up here."

Runtime findings are **single-machine and single-OS**: both execution personas share this host, so cross-platform install issues (a different OS, arch, or shell) are out of reach — state that. The two personas diverge on environment (cold vs warm caches) and posture (abandons-on-silence vs pushes-through), not platform. Timings are indicative, not benchmarks: one run each, on shared hardware.

Mitigations in force: web denied for all personas, flag-don't-resolve, predicted-output required, per-execution-persona isolation (scoped `HOME`/caches/tmp) so parallel installs don't collide, multi-persona aggregation. Treat findings as leads to verify with real users, not a substitute for them.
