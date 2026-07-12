# initiate-change

> A reusable skill that gates the *"let's change X"* event with a six-question seed
> and a four-artifact cascade. The upstream companion to
> [proof-before-done](https://github.com/ronrey/proof-before-done).

When an engineering author — human or AI agent — is about to open a change (an RFC, a
design doc, a migration plan, a refactor proposal, a new convention), this skill fires
and forces them to stand up a change directory and answer six specific questions in a
`seed.md` **before drafting the new design.** Each question targets a recurring failure
mode of under-considered change.

## What's in this repo

Three things you adopt together:

- **`SKILL.md`** — the portable skill. Copy it into your project's `.claude/skills/`
  directory (or your agent's equivalent), fill in four placeholders, and the gate is
  installed.

- **`CHANGE-ORDER-TEMPLATE.md`** — the `seed.md` shape. The six questions, with guidance
  under each on what a real answer looks like versus a restatement of the desire.

- **`MIGRATION-TEMPLATE.md`** — the migration variant. Same six questions, but Q6
  (reversibility) is promoted to first position because migrations live or die on their
  rollback surface, plus a migration-specific addendum (rollout window, rollback window,
  data-shape lock).

## Why this exists

Every deliberate change to a system starts with **noise** — a friction, a constraint, a
partner ask, a quality attribute that degraded. Noise without structure decays into
ad-hoc edits, "while I'm in here" scope creep, and architectural drift.

The failure mode this skill prevents is **drafting first**. When an author opens a
change conversation, the path of least resistance is to start sketching the new design.
The seed feels like overhead. It is not. Once the draft exists, the questions that
*should* have generated it — what caused this, what did we consider instead, how do we
know it worked, is it reversible — get retrofitted to justify the draft instead. The
decision was never actually made; it was rationalized after the fact.

The fix is a single rule: **the seed comes before the draft.** Hold the sketch. Write
the six answers. If the seed looks weak, the draft would have been weaker — you saved a
rewrite. If the seed looks strong, the draft lands on a foundation that survives review
six months later.

## What the skill is, structurally

initiate-change is the **upstream** half of a two-gate discipline:

| Stage | Gate | Fires at | What it gates |
|---|---|---|---|
| **Decide** | initiate-change | *let's change X* | Whether the proposal is well-formed enough to be built against |
| Do | (the work itself) | — | — |
| **Declare done** | [proof-before-done](https://github.com/ronrey/proof-before-done) | *this is done* | Whether the claim of done is well-formed enough to propagate |

Together they bracket every deliberate change: one gate at the moment you decide to
change something, one at the moment you claim it's finished. Both gates are structural,
not role-specific — an RFC drafted by an agent without a seed is the same failure mode
as one drafted by a human without one.

### The four-artifact cascade

A change directory holds four files, and each derives from the one above it:

```
<changes-dir>/YYYY-MM-DD-NNN-<slug>/
├── seed.md         # The discipline — six questions, the change's identity
├── plan.md         # The rollout — phases, sequencing, owners, dependencies
├── blueprint.md    # The contract — exact artifact shapes the executor cannot vary
└── playbook.md     # The execution log — what was done, when, by whom, with what result
```

- **seed** is the identity. Six questions; the artifact is the residue, not the goal.
- **plan** is the rollout — how it lands, in what order, against what dependencies.
- **blueprint** is the contract the executor cannot vary without re-entering the
  document. This is what prevents per-executor drift across a multi-repo change.
- **playbook** is the only artifact that lands *after* the work — the proof it happened,
  and where your done-gate fires at every step.

For a small change, only the seed needs filling; the other three stay one-line stubs.
For a large change, all four are used. One pattern carries every size — the author never
has to guess "is this a flat-file change or a directory change?" It is always a
directory; only the depth varies.

## How to adopt

Three steps:

1. **Copy the three files** into your project. The skill goes in your agent skill
   directory:
   ```
   <your-project>/.claude/skills/initiate-change/SKILL.md
   ```
   The two templates go wherever your change directories can reference them (e.g. a
   `templates/` folder next to your changes directory). For Codex or other agents,
   follow your agent's skill-installation convention.

2. **Fill in four placeholders** documented in the HTML comment at the top of
   `SKILL.md`:
   - `{{CHANGES_DIR}}` — where your change directories live (e.g. `docs/changes/`,
     `rfcs/`, `adr/`)
   - `{{TEMPLATE_PATH}}` — where the seed template lives, so the skill can point an
     author at it
   - `{{PROOF_SKILL}}` — your done-gate companion (use `proof-before-done` if you
     installed it)
   - `{{METHODOLOGY_DOC}}` — your team's engineering-standards doc, if any

3. **Delete the HTML comment block.** The skill should never ship with placeholder
   strings visible.

The full adoption guide — including what NOT to change — is in the comment block at the
top of `SKILL.md` itself.

## What this is not

- **Not a process replacement for thinking.** The discipline works because the author is
  genuinely answering the six questions, not filling fields. A team running this on
  autopilot will get autopilot change orders.

- **Not a guarantee the change is right.** The gate raises the floor of what reliably
  gets *considered* before it ships; it does not decide whether the change is a good
  idea. What changes is whether the alternatives, the blast radius, and the rollback
  surface were named *before* the draft hardened around them.

- **Not specific to AI agents.** Human engineers benefit from the same gate. The framing
  emphasizes agent-natural failure modes because that's where the seed is most often
  *skipped* — an agent will happily draft a beautiful RFC that answers none of the six
  questions — but the discipline is older than AI by decades.

- **Not a bureaucracy tax.** A small change's seed is four sentences and three stubs.
  The directory scales down as readily as it scales up; the cost is proportional to the
  change.

## Origin and provenance

Developed inside a multi-repository production codebase as the upstream companion to
[proof-before-done](https://github.com/ronrey/proof-before-done). Where proof-before-done
gates the *per-artifact* "this is done" event, initiate-change gates the *per-decision*
"let's change X" event. The four-artifact cascade (seed → plan → blueprint → playbook)
emerged from the observation that a single flat change-order file was the right shape for
small changes and the wrong shape for anything that needed a rollout plan, an executor
contract, and an execution log — so the directory generalizes the flat file, and one
pattern carries every size of change.

## License and attribution

Shared in the spirit that disciplines should be portable across teams. If you adopt it,
you don't owe attribution. If you find it useful and adapt it further, sharing your
adaptations back (or upstream via a pull request) helps the next team.

## Contributing

This repository is the canonical public home of the skill. If you find bugs in the
template (broken cross-references, unclear placeholder documentation), please open an
issue or send a pull request.

If you adopt the skill and want to share what you learned — which failure modes the seed
caught for your team, how you wired the change labels into your PR process, how the
cascade scaled for a large migration — those notes are welcome. The discipline gets
stronger as more teams run it.

## Cross-references

- `SKILL.md` — the skill (the part you adopt)
- `CHANGE-ORDER-TEMPLATE.md` — the seed template (the six questions)
- `MIGRATION-TEMPLATE.md` — the migration variant (Q6 first, plus the addendum)
- [proof-before-done](https://github.com/ronrey/proof-before-done) — the companion skill
  at the artifact-done gate
