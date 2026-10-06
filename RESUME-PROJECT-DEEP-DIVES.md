# Resume Project Deep-Dives

Purpose: for resume bullets describing a real architectural decision, capture the honest
technical justification here so it's ready to defend under a real follow-up question, not just
asserted and hoped it doesn't get probed. Add a new section per project as they come up (the
flagship portfolio project and system design case studies will generate more of these over time).

## Fractional architecture work at a VC-funded startup (SOC2 in progress — placeholder, flesh out as it develops)

**The resume bullet (draft):** Fractional Software Architect, VC-funded startup, ~8+ months —
architecting toward SOC2 Type II compliance to support enterprise sales, including a named
enterprise prospect (MassMutual).

**Why it's a strong signal:** the combination of (a) real architectural responsibility in a
resource-constrained, ambiguous environment, (b) external validation via actual VC funding, not
just a self-funded, unvalidated side project, (c) done concurrently with a full-time job,
demonstrating genuine commitment rather than a hobby, and (d) bridges the Coupa security/
compliance exposure — disliked personally, but real literacy gained — into a context where it
now pays off directly in actual engineering work.

**The SOC2 angle specifically:** SOC2 compliance is very likely the actual gating factor for a
financial services company like MassMutual to even evaluate a startup vendor in the first place.
That makes "architected controls supporting SOC2 Type II compliance, directly enabling enterprise
prospect evaluation" a concrete, causally-connected business story — compliance work tied to a
real outcome, not a checkbox exercise.

**Precision matters — don't overclaim before it's actually true:** "architected controls toward
SOC2 Type II compliance" is accurate for in-progress work. "Achieved SOC2 certification" should
only be used once an actual audit is complete. Claiming the stronger version early is exactly the
kind of thing that unravels under one specific interview follow-up.

**Fill in once further along:**
- [ ] Which specific controls/architecture decisions were made (access control design, encryption
      standards, logging/monitoring, change management process, vendor risk management, incident
      response procedures)
- [ ] Current status of the actual audit (Type I vs. Type II, in progress vs. complete, auditor
      engaged or not)
- [ ] Any quantifiable connection to the MassMutual deal specifically (e.g., "SOC2 readiness was
      cited as a requirement in the MassMutual evaluation")

## Test drive cube (in-person kiosk, Carvana)

**The resume bullet:** built the backend for Carvana's test drive cube — a four-display physical
kiosk at test drive centers where customers browse current inventory over a WebSocket connection,
get matched to an available test drive vehicle based on their selection, and get a generated
walking path to that vehicle.

**Don't undersell this one just because it feels less complex than the workflow engine** — it's a
different, and genuinely interesting, kind of problem: real-time, physical, and with a live
concurrency challenge instead of a pure backend/data problem.

**The architecture tradeoff worth explaining:** state is kept in-pod (local memory) rather than
in a distributed store like Redis or Cosmos DB, with Azure Service Bus used to broadcast
state-change events so every pod's local memory converges — an eventually-consistent,
event-propagated cache instead of a shared external store. That's a real CAP-style tradeoff:
favoring low latency (no network round-trip to an external store, which matters for a kiosk UX
where a customer is standing there waiting) and operational simplicity (no separate distributed
cache infra to run) over strong consistency — justified specifically because the tracked data is
low-scale and ephemeral (relevant only for the life of an active session at a physical location),
so eventual convergence and loss-on-restart are both acceptable costs.

**The concurrency question, resolved — and it's a better answer than a distributed lock:**
customers pick a vehicle from the merchandise site (make/model/options), which gets fuzzy-matched
to an actual physical vehicle on the lot — not the literal unit they browsed. For most
make/model/option combinations there's only one matching physical vehicle on the lot anyway, so
multiple customers *can* legitimately match to "the same vehicle" in software terms, and that's
fine — the real constraint (only one person can physically test-drive a specific car at a time)
is enforced operationally by staff on the lot, not by the software. Deliberately not building a
distributed lock/reservation system here is the right call, not a gap: the actual scarce resource
is physical and human-mediated, so a software-level consistency mechanism would have been solving
a problem that doesn't exist at that layer. **Say in an interview:** "We recognized the race
condition wasn't actually a software problem — the physical test-drive slot is the real
constraint, and that's already mediated by staff on the lot. Building a distributed lock for it
would have been unnecessary complexity for a constraint the system doesn't actually own." That
kind of "we identified what not to build" answer reads as stronger judgment than a clever locking
scheme would have.

**Why it's sellable despite feeling smaller:** it's physical and memorable (interviewers remember
an unusual kiosk/IoT-adjacent project more than another backend API), it's directly tied to
Carvana's core revenue path (getting customers into the right test drive quickly), and it
demonstrates real-time/event-driven systems breadth that complements the more traditional
enterprise-SaaS flavor of the workflow engine story — range, not just repetition of one skill.

## Vehicle/inventory CMS (data layer behind the test drive cube)

**The resume bullet:** built the internal CMS used by content and operations teams to manage
vehicle catalog data, marketing copy, and location-specific test-drive vehicle availability —
the reference data store loaded by the cube's matching algorithm.

**The architecture point worth naming explicitly:** this is a deliberate three-layer separation
of concerns, not one system doing everything:
1. **Catalog data** (vehicle specs, copy) — relatively slow-changing, global across locations.
2. **Location-specific availability** — which physical vehicles are actually present and
   drivable at a given test drive center right now, distinct from the general Carvana stock
   catalog.
3. **Real-time session/event layer** — live customer interactions, handled by a separate UI and
   the same Service Bus event mechanism documented in the test drive cube entry above.

The pods combine all three to perform the actual customer-to-vehicle matching: catalog data for
what a vehicle *is*, location availability for what's actually *there*, and real-time events for
what a *customer is doing right now*. That's a clean, purposeful split — cold/slow-changing
reference data kept separate from hot/real-time transactional state — rather than one system
overloaded with both.

**Say in an interview:** "We split the system into three logical layers instead of one flat
data store: a CMS for slow-changing catalog data and copy, a location-specific availability
layer for which vehicles were physically present at a given center, and a real-time event layer
for live customer sessions. The pods loaded the first two and combined them with the third to
do the actual matching."

**Ties back to the earlier judgment call:** the location-availability data being managed through
the CMS (staff/ops input) rather than enforced through a software lock reinforces the same point
made in the test drive cube entry — the physical constraint is genuinely operational, not a
software consistency problem, and the system design reflects that rather than fighting it.

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
