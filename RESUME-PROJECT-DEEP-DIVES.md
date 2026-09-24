# Resume Project Deep-Dives

Purpose: for resume bullets describing a real architectural decision, capture the honest
technical justification here so it's ready to defend under a real follow-up question, not just
asserted and hoped it doesn't get probed. Add a new section per project as they come up (the
flagship portfolio project and system design case studies will generate more of these over time).

## In-house workflow engine (vs. Temporal / Camunda)

**The resume bullet:** built an in-house workflow engine because existing options (Temporal,
Camunda) didn't fit the requirements.

**The honest, scoped justification** — this is a narrow, shape-specific claim, not "in-house is
generally better." Don't oversell it as the latter; the scoped version is both more accurate and
more credible in an interview:

Temporal's code-first execution model assumes the workflow's control-flow shape is fixed at
deploy time — it deterministically replays known control-flow paths. It has no first-class
concept of the task graph itself being per-customer runtime data. The actual requirement was: the
same set of composable tasks, but the ordering and branching rules vary per customer and are
configured, not coded. That's a genuine structural mismatch with Temporal's determinism model,
not a preference — building on top of Temporal would have meant fighting its model to bolt on a
data-driven graph layer anyway. Building the rules/task layer in-house, with idempotency as a
first-class invariant, was the more direct fit for that specific requirement.

**Say in an interview:** "We evaluated Temporal and Camunda first. The blocker wasn't tooling
maturity — it was a structural mismatch. Temporal assumes the workflow's control-flow shape is
fixed at deploy time and replays known paths deterministically. Our actual requirement was a
per-customer, runtime-configurable task graph — the same composable tasks, but ordering and
branching rules varied by customer and were configured, not coded. That's not something
Temporal's determinism model supports natively, so we'd have been building a data-driven graph
layer on top of it regardless. Building it ourselves, with idempotency as a first-class
invariant, was the more direct fit."

**Business driver (the "why it mattered commercially," not just technically):** in an EdTech
context with a wide range of customer integrations, very few customers ran genuinely identical
end-to-end workflows — but the individual steps were highly repeated and idempotent across
customers. The business couldn't force customers into one fixed, defined flow given how much
their systems/integrations varied, but could onboard them quickly by supporting a general set of
step-wise building blocks and just runtime-configuring how those steps composed per customer.
That's the commercial reason the runtime-configurable graph mattered — it turned onboarding a new
customer into a configuration exercise instead of a bespoke development effort each time.

**Quantified performance impact:** the prior mechanism was SFTP polling with directory-based
batch processing, taking 30-60 minutes to fully process a customer order. The new pipeline
processes an order in under 2 seconds — roughly a 900-1800x latency improvement. Concrete,
quantified numbers like this are worth leading with in an interview before getting into the
architecture discussion — they're the kind of impact statement that lands immediately, before
the technical justification even comes up.

**Organizational impact:** the engine was generalized into a reusable library adopted across the
company, not a one-off solution for a single team. Worth naming explicitly in an interview — it's
direct evidence of platform-level thinking (building something for reuse beyond your own team's
immediate problem), which is exactly the kind of scope a platform/architecture-flavored Senior
role is looking for, distinct from "I fixed a problem for my team."

**Don't overclaim:** this justifies in-house *for that specific problem shape* — fixed pipelines
(e.g., "export NPS," "build a JDP feed") are exactly the shape Temporal/Camunda fit well. If
asked "would you build in-house again," the honest answer is "depends on whether the task graph
itself needs to be runtime/per-tenant data, versus the pipeline shape being fixed at deploy
time" — not a blanket "existing tools weren't good enough." The narrower, more qualified answer
is the one that actually holds up under a sharp technical interviewer, and reads as better
judgment than the sweeping version would.
