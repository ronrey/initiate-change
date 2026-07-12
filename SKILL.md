<!--
============================================================================
ADOPTION GUIDE — read this block, fill the placeholders, then DELETE this block.
============================================================================

initiate-change is a portable agent skill. It fires at the "let's change X"
event and forces the author (human or agent) to stand up a change directory and
answer six questions in a seed file BEFORE drafting the new design, contract, or
migration. It is the upstream companion to proof-before-done
(https://github.com/ronrey/proof-before-done), which fires at the "this is done"
event.

TO INSTALL
----------
1. Copy this file to your project's agent skill directory:
     <your-project>/.claude/skills/initiate-change/SKILL.md
   (For Codex or other agents, follow your agent's skill-installation convention.)

2. Copy the two templates alongside it (or anywhere your change directories can
   reference them):
     CHANGE-ORDER-TEMPLATE.md   — the seed.md shape (six questions)
     MIGRATION-TEMPLATE.md      — the migration variant (Q6 promoted to first)

3. Fill the FOUR placeholders below, everywhere they appear in this file:

     {{CHANGES_DIR}}      — where your change directories live, repo-relative,
                            trailing slash. Examples: "docs/changes/",
                            "docs/noise/changes/", "rfcs/", "adr/".

     {{TEMPLATE_PATH}}    — where the seed template lives, so the skill can point
                            an author at it. Example:
                            "docs/changes/templates/CHANGE-ORDER-TEMPLATE.md".

     {{PROOF_SKILL}}      — how your project refers to its done-gate companion, if
                            you run one. If you installed proof-before-done, use
                            "proof-before-done". If you have no done-gate, write
                            "your done-gate" and trim the pairing section.

     {{METHODOLOGY_DOC}}  — your team's engineering-standards or methodology doc,
                            if any, that explains WHY this discipline produces the
                            results it does. If none, delete the line that cites it.

4. Delete this entire comment block. The skill should never ship with placeholder
   strings visible.

WHAT NOT TO CHANGE
------------------
- The six questions. They are the discipline. Renaming or dropping one breaks the
  gate.
- The "seed before draft" rule. That is the load-bearing constraint.
- The four-artifact cascade (seed -> plan -> blueprint -> playbook). You may leave
  plan/blueprint/playbook as stubs for small changes, but the shape stays.

You MAY change: the directory path, the number format, label names, cross-
reference paths, and any prose that is specific to your team's conventions.
============================================================================
-->
---
name: initiate-change
description: Use BEFORE producing any architectural artifact (RFC, design doc, migration plan, change order, spec, refactor proposal, infra change, new convention). The activation-function gate at the *let's change X* event — a four-artifact cascade per change. Triggers on phrases like "let's redesign", "we need to change", "I want to migrate", "rewrite the X layer", "introduce a new convention", "deprecate Y", "the contract for Z needs to move", or any opening of an RFC / design doc / migration plan. Pairs with {{PROOF_SKILL}} at the artifact-done gate.
---

# initiate-change

## Why this exists

Every deliberate change to a system starts with **noise** — a perturbation, a felt friction, a constraint that surfaced, a partner ask, a quality attribute that degraded. Noise without structure decays into ad-hoc edits, "while I'm in here" scope creep, and architectural drift. Noise with structure becomes a generating seed that produces a living change.

This skill is the activation function at the *let's change X* event. It says: **before you draft a single line of the new design, the new contract, or the new migration, you stand up the change directory and you fill the four artifacts in order.** The four artifacts are the cascade — seed produces plan produces blueprint produces playbook — and they are how a single sentence of intent becomes a shippable, reviewable, reversible change.

A single flat file is the right shape for a *small* change. It is the wrong shape for a change that needs a plan, a blueprint, and a playbook. A directory generalizes the convention so one pattern carries every size of change — the author never has to guess "is this a flat-file change or a directory change?" It is always a directory; only the depth varies.

## What gets created — the change directory shape

For every deliberate change, create a directory:

```
{{CHANGES_DIR}}YYYY-MM-DD-NNN-<slug>/
├── seed.md         # The discipline — six questions, the change's identity
├── plan.md         # The rollout — phases, sequencing, owners, dependencies
├── blueprint.md    # The contract — exact artifact shapes the executor cannot vary
└── playbook.md     # The execution log — what was done, when, by whom, with what result
```

Optional companions when warranted (not required):

- `README.md` or `INDEX.md` — one-paragraph orientation pointing at the four artifacts
- `notes/` — research, source material, conversation transcripts, partner emails
- `artifacts/` — diagrams, screenshots, schemas, migration scripts referenced by the four files

### Directory-name rules

| Component | Rule |
|---|---|
| `YYYY-MM-DD` | The date the seed was authored (ISO format, no slashes). Not the date the change ships. |
| `NNN` | Three-digit zero-padded sequential number, **repo-global**, monotonic across all changes ever authored in this repo. Look at the highest existing number and add one. Never reuse, never reset by year. |
| `<slug>` | Kebab-cased Q1 answer from the seed. Truncate to 60 chars if needed. The slug carries the change's identity at the directory level. |

**Examples**:
- `2026-05-26-001-promote-checkout-api-to-ga`
- `2026-06-04-007-migrate-decision-engine-to-postgres-events`
- `2026-06-12-013-replace-zod-with-valibot-across-services`

### Why a directory, not a flat file

A flat file optimizes for the smallest change. A directory optimizes for the change *as it grows*. Small changes may only need `seed.md` populated, with `plan.md`/`blueprint.md`/`playbook.md` left as one-line stubs ("not required — change is small enough that seed.md is the full record"). Large changes use all four. The structure is the same in both cases; only the depth varies.

## The four artifacts — what each one does

The four files form a **cascade**: each one derives from the one above it and constrains the one below. Each one passes its closing test before the next one starts.

### 1. `seed.md` — the discipline

The seed answers the six questions of the change-order gate. The seed is the **identity** of the change. Every other artifact is downstream of it. Fill it from the seed template at `{{TEMPLATE_PATH}}`.

```markdown
---
type: change-order
created: YYYY-MM-DD
status: draft | in-review | approved | in-flight | shipped | withdrawn
triggers: <one or more — contract / interface / invariant / shape>
---

# <one-sentence Q1 answer, repeated as the title>

> Authored against the change-order discipline. Six questions; the artifact is the residue, not the goal.

## Q1. What is the change, in one sentence?
## Q2. Why is this change being made?
## Q3. What does this change touch — and what does it NOT touch?
## Q4. What was considered instead, and why was this chosen?
## Q5. How will we know the change worked — and how will we know it didn't?
## Q6. Is this change reversible? If not, what is the rollback surface?

## Closing test

- [ ] A staff engineer who was not in the conversation can read this seed six months from now and reconstruct why the change was made, what it touched, and what its alternatives were.
```

For migrations, Q6 is promoted to first position (migrations live or die on reversibility) and a migration-specific addendum follows: rollout window, rollback window, data-shape lock. See `MIGRATION-TEMPLATE.md` for the full shape.

**The seed alone is sufficient for small changes.** If `plan.md`, `blueprint.md`, and `playbook.md` are not needed, leave them as one-line stubs naming why. The seed is non-negotiable.

### 2. `plan.md` — the rollout

The plan answers: *how does this change land, in what order, against what dependencies, with what owners?* The plan is downstream of the seed — every phase, every dependency, every owner traces back to a question the seed answered.

A plan has these sections (minimum):

- **Phases** — numbered, dated, with acceptance criteria each
- **Dependencies** — what must land before each phase
- **Owners** — who authors and reviews each phase
- **What this plan does NOT solve** — the Q5 discipline at the rollout layer

The plan does not have to be long. A two-phase plan with three sentences per phase is a complete plan if the change is small. The structure is the same; the depth scales.

### 3. `blueprint.md` — the contract

The blueprint closes every decision the plan deferred. The blueprint is the **contract the executor (engineer, agent, install-PR author) cannot vary without re-entering this document.** This is the artifact that prevents per-executor drift.

The blueprint names:

- Exact artifact shapes (template structures byte-for-byte, file paths, naming conventions)
- Exact prose where prose is load-bearing (doc insertions, PR template lines, README sections)
- Exact label sets, exact code-ownership rules, exact CI configuration
- Acceptance criteria per phase — what counts as done, not just what is intended
- What the executor is *allowed* to change (grammar, link paths, sequencing) vs. *not allowed* to change (the six questions, the seed itself, the discipline)

If the executor surfaces a finding that requires changing the blueprint, the change lands in the blueprint first, then the executor re-derives. This is the seed → plan → blueprint → execution loop's correctness condition.

### 4. `playbook.md` — the execution log

The playbook is the **proof the change actually happened.** It is the only artifact that lands *after* the work, not before. The playbook records:

- What was done, in what sequence
- When each step landed (commit, PR, deploy)
- Who did it
- What broke, what was fixed, what was learned
- Which acceptance criteria from `plan.md` and `blueprint.md` were met, with verification
- Which were deferred, with reason
- **What this execution does NOT close** — the Q5 discipline at the execution layer

The playbook is where the done-gate ({{PROOF_SKILL}}) fires at every step. Every "step landed" entry in the playbook must survive the done-gate. The playbook is the residue of the change; six months from now, it is what a staff engineer reads to understand what actually happened.

## How to apply

When the user opens a *let's change X* conversation:

1. **Recognize the trigger.** Phrases like "let's redesign", "we need to change", "I want to migrate", "rewrite the X layer", "introduce a new convention", "deprecate Y", "the contract for Z needs to move" — these are change-initiation triggers. Also: any opening of an RFC, design doc, migration plan, architectural proposal, or "new way of doing X."
2. **Pause before drafting.** Do not draft the design, the contract, or the migration first. The seed comes first. The temptation to "just sketch it" is the failure mode this skill prevents — sketches harden into the design and the six questions never get answered honestly.
3. **Compute the next NNN.** Scan `{{CHANGES_DIR}}` **recursively** (create the directory if it does not exist) — nested child changes hold numbers too, so `find {{CHANGES_DIR}} -type d -name '20*'` is the ledger, not a top-level `ls`. Find the highest three-digit prefix anywhere in the tree, increment. If empty, start at `001`. **After creating the directory, verify uniqueness**: a glob for `*-NNN-*` across the tree must match exactly your new directory — two matches means another session took the number first; renumber yours before writing anything else. (Concurrent sessions are the common cause of number collisions; this post-create check is what catches them.)
4. **Compute the slug.** Ask the user (or yourself) for the one-sentence Q1 answer. Kebab-case it. Truncate to 60 chars.
5. **Create the directory** at `{{CHANGES_DIR}}YYYY-MM-DD-NNN-<slug>/`.
6. **Write `seed.md` first.** Answer all six questions in prose. No "n/a", no single-word answers, no fillable fields. Run the closing test before moving on.
7. **Write `plan.md` next** if the change has multiple phases or dependencies. If not, stub it.
8. **Write `blueprint.md` next** if the executor needs a contract (i.e., the change spans multiple files, repos, or commits). If not, stub it.
9. **Stub `playbook.md`** with a header and the date. The playbook fills in as the work proceeds.
10. **Cross-link.** Add a one-line index entry to `{{CHANGES_DIR}}INDEX.md` (create if missing) pointing at the new directory. PRs touching the change link the directory in the PR body.

### The change-order labels on PRs

PRs that implement work landed against a change directory carry one of the four change-order labels:

- `change-order:contract` — API surface, message schema, event payload, public function signature
- `change-order:interface` — UI surface, CLI surface, configuration surface, public-facing artifact
- `change-order:invariant` — uptime, ordering, idempotency, isolation, latency-floor invariant
- `change-order:shape` — architecture, data model, service topology, infrastructure

The label fires the requirement to link the change directory in the PR body. Reviewer rejects a labeled PR without a linked change directory.

## The discipline of *not drafting first*

The single most important rule in this skill: **the seed comes before the draft.**

When an agent (or a human) opens a *let's change X* conversation, the path of least resistance is to start drafting the new design. The seed feels like overhead. It is not overhead. It is the gate that prevents the change from being *fake*-considered — sketched well enough to look like a decision, but missing the cause, the alternatives, the rollback surface, and the failure criterion. Once the draft exists, the six questions get retrofitted to the draft instead of generating the draft. That is the failure mode.

The fix: hold the draft. Write the seed. Re-read the seed. If the seed looks weak, the draft would have been weaker — you saved yourself a rewrite. If the seed looks strong, the draft now has a foundation that survives the closing test.

## Closing test for the skill

Ask aloud, before any architectural draft propagates:

> **"Have I stood up the change directory and written `seed.md` to closing-test pass before drafting the design?"**

- If no → you are skipping the gate. Stop. Stand it up.
- If yes → you have noise with structure. Proceed to the draft.

## Anti-patterns this skill prevents

| Anti-pattern | What it looks like | Why it fails |
|---|---|---|
| **Drafting first** | RFC body written before any change directory exists | The six questions get retrofitted to justify the draft instead of generating it |
| **Flat-file regression** | A single `.md` for a multi-phase change | The plan/blueprint/playbook get scattered or never written — the cascade is lost |
| **Empty seed** | `seed.md` exists but the answers are "n/a" or one-word | The closing test fails — a future reader cannot reconstruct the change |
| **Skipped blueprint** | Multi-repo change without a `blueprint.md` | Executors drift across repos — the same "install" produces five different shapes |
| **Missing playbook** | Change ships but no `playbook.md` records what actually happened | Six months later, a staff engineer cannot tell what the executor did vs. what the plan asked for |
| **Slug collision** | Two directories with the same date and slug | NNN exists precisely to prevent this — if you collided, you skipped step 3 |
| **Number collision** | Two directories share the same NNN | Step 3's recursive scan + post-create uniqueness check exists to prevent this — max+1 computed from a top-level `ls`, or never re-verified, collides on concurrent sessions |
| **Mid-flight blueprint edits** | Executor changes the blueprint to match what they did | The blueprint is the contract; if execution diverged, the divergence lands in the blueprint *first*, then re-derives |

## The pair with the done-gate

This skill fires at the **per-decision** *let's change X* event. A done-gate ({{PROOF_SKILL}}) fires at the **per-artifact** *this is done* event. They are the upstream and downstream of every deliberate change:

| Stage | Skill | Gate fires at | What it gates |
|---|---|---|---|
| Decide | initiate-change | *let's change X* | Whether the proposal is well-formed enough to be built against |
| Do | (the work itself) | — | — |
| Declare done | {{PROOF_SKILL}} | *this is done* | Whether the claim of done is well-formed enough to propagate |

Both gates apply to both human and agent authors. An RFC drafted by an agent without a `seed.md` is the same failure mode as an RFC drafted by a human without one — the gate is structural, not role-specific.

## Cross-references

- `CHANGE-ORDER-TEMPLATE.md` — the canonical `seed.md` template, full text
- `MIGRATION-TEMPLATE.md` — the migration variant (Q6 promoted, addendum follows)
- proof-before-done — the companion skill at the artifact-done gate: https://github.com/ronrey/proof-before-done
- `{{METHODOLOGY_DOC}}` — your team's engineering-standards doc, if any, that explains why this discipline produces the results it does
