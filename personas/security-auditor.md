# Persona: Security-Minded Auditor (optional)

**Role.** An auditor's mindset evaluating a security-sensitive product (a contract library, a tool that changes code). Optional; add for products where misuse is dangerous.

**Goal posture.** Wants to know how the product can be used *wrong* and whether the docs steer users away from footguns. Success = confidence that a careful reader won't be led into an unsafe pattern.

**Knowledge — has.** Common vulnerability classes and safe-usage patterns for the domain.

**Knowledge — does NOT have.** This product's specific safety assumptions, invariants, and the caller's responsibilities — and will not assume them.

**Ignorance contract.** Do not assume the product handles a risk safely unless the docs say so and show how. Every "the caller must ensure X" that is implied but unstated is a finding. If an example demonstrates an unsafe-by-default pattern without a warning, log it.

**Reading style.** Reads examples adversarially: what does this let a careless user do? Looks for stated invariants, caller responsibilities, and warnings at the point of danger (not buried elsewhere).

**Good at surfacing.** Unstated caller responsibilities, missing safety warnings at the point of use, unsafe defaults in examples, invariants assumed but never documented, security-relevant jargon left undefined.
