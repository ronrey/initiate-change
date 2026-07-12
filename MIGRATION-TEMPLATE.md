---
type: change-order
subtype: migration
created: <YYYY-MM-DD>
status: draft | in-review | approved | in-flight | completed | rolled-back
triggers: <list one or more — contract / interface / invariant / shape>
migration-class: <schema | api-version-cut | infra-replatform | dependency-major | other>
# Optional nesting fields — for a migration that decomposes into child changes.
# kind: leaf              # leaf (default) or parent
# parent: <parent-slug>   # required when this migration is nested under a parent (omit for top-level migrations)
# children:               # required when kind: parent — list child slugs nested inside this migration's directory
#   - <child-slug-1>
#   - <child-slug-2>
---

# <one-sentence Q1 answer, repeated as the title>

> Authored using the migration template. Q6 is first because migrations live or die on reversibility. The remaining five questions follow the canonical order. The migration-specific addendum at the bottom names the operational facts the canonical questions do not name with enough specificity.

## Q6. Is this migration reversible? If not, what is the rollback surface?

<Read the canonical Q6 guidance first. For migrations specifically: name the exact backfill / rollback / version-flag mechanism. If the migration is irreversible past a specific cutover point, name the cutover point and what makes it irreversible. "Pre-cutover reversible, post-cutover irreversible" is a valid answer if both halves are specified.>

## Q1. What is the migration, in one sentence?

<One sentence. As with the canonical template — if you need two sentences, you have two migrations.>

## Q2. Why is this migration being made?

<Same guidance as the canonical Q2. For migrations: include the cost of NOT migrating (the "do nothing" path's failure mode is usually the strongest cause).>

## Q3. What does this migration touch — and what does it NOT touch?

<Same guidance as the canonical Q3. For migrations: be specific about which services, which data stores, which downstream consumers, and which integrations are touched in the migration window vs. permanently. A migration touches things temporarily AND permanently; both lists matter.>

## Q4. What was considered instead, and why was this chosen?

<Same guidance as the canonical Q4. For migrations: "live-with-the-old-shape" is always a valid alternative to consider; if it was rejected, name why.>

## Q5. How will we know the migration worked — and how will we know it didn't?

<Same guidance as the canonical Q5. For migrations: separate "mid-flight health" criteria from "post-cutover success" criteria. Both are observable; both need to be named in advance.>

## Migration-specific addendum

### Rollout window

<Named start time, named end time, named cutover point if applicable. "When ready" is not a window.>

### Rollback window

<How long after cutover can the migration be rolled back, and by what mechanism. If the rollback window is "zero" — irreversible at cutover — say so explicitly. This pairs with Q6.>

### Data-shape lock

<What data shape is locked in at the migration's completion. Future change-orders that touch the same shape reference this section so the lock is visible.>

## Closing test

> The closing test below is the **leaf** variant — the default. For a parent migration (`kind: parent`), use the **parent variant** in the block beneath this one instead, which adds the union-of-artifacts gate.

- [ ] A staff engineer who was not in the conversation can read this migration order six months from now and reconstruct why the migration was made, what it touched, what its alternatives were, and what its rollback surface is.

### Parent variant — use when `kind: parent`

- [ ] Every child has shipped its own closing test.
- [ ] The parent's six questions are discharged by the union of all four artifacts of all children plus the parent's own four artifacts. Substituting the first checkbox for this one is the mission-rot failure mode.

If the box (or boxes, for the parent variant) is unchecked, the migration order is not done. Rewrite until it is.

## Cross-references

- (Link any related change orders, RFCs, PRs, issues, or partner conversations)
