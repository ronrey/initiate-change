---
type: change-order
created: <YYYY-MM-DD>
status: draft | in-review | approved | shipped | withdrawn
triggers: <list one or more — contract / interface / invariant / shape>
# Optional nesting fields — for a change that decomposes into child changes.
# kind: leaf              # leaf (default) or parent
# parent: <parent-slug>   # required when this change is nested under a parent (omit for top-level changes)
# children:               # required when kind: parent — list child slugs nested inside this change's directory
#   - <child-slug-1>
#   - <child-slug-2>
---

# <one-sentence Q1 answer, repeated as the title>

> The six questions below are the discipline; their answers below are the change order. If any answer reads as "n/a" or a single word, the question has not been answered — rewrite until it has.

## Q1. What is the change, in one sentence?

<One sentence. If you need two, you have two changes — split them or sharpen this one. This sentence is the change's identity; future readers will evaluate every other answer against it.>

## Q2. Why is this change being made?

<Cause. The upstream pressure that produced the proposal — a constraint, a measurement, a partner ask, a quality attribute that degraded. Include who carries the authority for the change and which quality attribute is being optimized at what cost. "Because we need to" is a restatement of the desire, not a cause.>

## Q3. What does this change touch — and what does it NOT touch?

<Name every surface, service, contract, downstream consumer, upstream source, and dependent invariant the change reaches. Then name what is explicitly out of scope. Hidden blast radius is the most expensive failure mode of change; visible blast radius is the cheapest review.>

## Q4. What was considered instead, and why was this chosen?

<At least one named alternative, with the specific reason this change is preferred over it. "We didn't consider alternatives" is valid only when accompanied by the reason no alternatives were viable.>

## Q5. How will we know the change worked — and how will we know it didn't?

<A success criterion and a failure criterion. Both observable before the change ships, both measurable after. "We'll see how it goes" is not a criterion. If success and failure look identical from the outside, the change is structurally undetectable.>

## Q6. Is this change reversible? If not, what is the rollback surface?

<If reversible: name how — feature flag, version flag, deployment rollback, data backfill, the specific mechanism. If irreversible: name that explicitly. Irreversibility is not forbidden; it has to be recognized at decision time, not at regret time.>

## Closing test

> The closing test below is the **leaf** variant — the default. For a parent change (`kind: parent`), use the **parent variant** in the block beneath this one instead, which adds the union-of-artifacts gate.

- [ ] A staff engineer who was not in the conversation can read this change order six months from now and reconstruct why the change was made, what it touched, and what its alternatives were.
- [ ] **Echo sweep** — if this change supersedes or corrects a fact, number, or frame, the corpus has been grepped for the superseded phrasing and every live doc repaired (or listed here as intentionally historical). A correction that lands in one canonical spot while the old fact stays live elsewhere is not closed.
- [ ] **The change directory is committed** — `git status` shows nothing untracked under this change's directory. Docs that exist only on one machine are not an audit trail.

### Parent variant — use when `kind: parent`

- [ ] Every child has shipped its own closing test.
- [ ] The parent's six questions are discharged by the union of all four artifacts of all children plus the parent's own four artifacts. Substituting the first checkbox for this one is the mission-rot failure mode.

If the box (or boxes, for the parent variant) is unchecked, the change order is not done. Rewrite until it is.

## Cross-references

- (Link any related change orders, RFCs, PRs, issues, or partner conversations)

## What this does NOT solve

- <Name what `<one-sentence Q1 answer>` does NOT cover — the boundary that makes the claim falsifiable. This is the scope-boundary statement; without it the change has a closing test and citations but no falsifiable edge.>
