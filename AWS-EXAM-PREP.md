# AWS Solutions Architect Professional (SAP-C02) — Exam Prep

Starting position: already ~80% there from the hands-on Terraform/AWS build from scratch.
Strategy is **diagnostic-first, not course-first** — don't re-study things already known cold;
find the actual gaps, then close only those.

## Step 1: Cold diagnostic practice exam

Before any fresh studying, take one full-length, timed practice exam under real exam
conditions. This tells you where the remaining ~20% actually is, instead of guessing.

- **Tutorials Dojo (Jon Bonso)** — the practice exam set most consistently recommended by people
  who've passed SAP-C02. Realistic difficulty, strong per-question explanations.
- Score it honestly, timed, no notes — treat it as the real thing.

## Step 2: Map results against the official exam guide

Download the official AWS Exam Guide (PDF, free on AWS's certification page) — it lists the
exact domains, task statements, and in-scope services. Cross-reference your diagnostic misses
against it to see exactly which task statements are weak, not just "AWS networking is kind of
fuzzy."

Approximate domain breakdown (verify current weights on the official guide — these shift
between exam versions):
- Design Solutions for Organizational Complexity (~26%)
- Design for New Solutions (~29%)
- Continuous Improvement for Existing Solutions (~25%)
- Accelerate Workload Migration and Modernization (~20%)

## Step 3: Target only the weak spots

Likely candidates given the hands-on-Terraform background specifically (operational AWS-native
knowledge that a from-scratch build doesn't naturally cover, as opposed to architecture
reasoning, which is probably already solid):
- [ ] AWS Organizations / Control Tower — multi-account governance patterns
- [ ] Migration-specific services — DMS, Application Migration Service (MGN)
- [ ] Cost-optimization specifics — Savings Plans vs. Reserved Instances, Cost Explorer/Budgets patterns
- [ ] Networking edge cases — Transit Gateway, Direct Connect, cross-region/cross-account patterns
- [ ] Anything else the diagnostic surfaces

For each weak spot: read the relevant AWS whitepaper/FAQ page directly rather than a full course
module — professional-level questions pull heavily from AWS's own architectural best-practice
guidance (the Well-Architected Framework whitepaper is a particularly high-value read).

**If a domain comes back broadly weak, not just a couple of services:** only then pull in a
structured course (Adrian Cantrill's or Stephane Maarek's SAP-C02 courses are both well-regarded)
for that specific domain — not the whole course linearly from the start.

## Step 4: Repeat practice exams, track the trend

Keep taking full, timed practice exams as gaps close — both to verify the weak spots are
actually fixed and to build pacing stamina. SAP-C02 is 180 minutes and scenario-heavy; time
management is as much a skill here as the content knowledge.

| Attempt | Date | Score | Weakest domain this time |
|---|---|---|---|
| Diagnostic | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |

## Step 5: Schedule the exam

Once practice scores are consistently well above the passing bar (not just barely over it —
real exam difficulty and anxiety both cost a few points), schedule the actual exam. Per
`STUDY-PLAN.md` Phase 1, the goal is to have this done in the first 1-2 months, while the
existing hands-on knowledge is freshest and before AZ-104/AZ-305 study ramps up.

## Resources

- Tutorials Dojo SAP-C02 practice exams
- AWS Skill Builder (free, official) — exam-prep content and additional official practice questions
- Official AWS SAP-C02 Exam Guide (PDF)
- AWS Well-Architected Framework whitepaper
- Adrian Cantrill / Stephane Maarek SAP-C02 courses — only for domains that come back broadly weak
