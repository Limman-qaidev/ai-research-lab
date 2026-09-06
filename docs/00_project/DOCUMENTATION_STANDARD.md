# DOCUMENTATION_STANDARD.md

## 1. Goal

The documentation system exists to preserve **understanding, decisions, reproducibility, and continuity** without turning the repository into a transcript archive.

Core rule:

> Document knowledge, not conversations.

## 2. Document types

### Stable documents

Change rarely:

- `PROJECT_CHARTER.md`
- `LEARNING_CONTRACT.md`

Changes should be deliberate and, when material, recorded in the decision log.

### Planning/state documents

Change as the project evolves:

- `ROADMAP.md`
- `CURRENT_STATE.md`
- `OPEN_QUESTIONS.md`
- `RESEARCH_IDEAS.md`

### Historical documents

Append or supersede rather than rewrite silently:

- `DECISION_LOG.md`
- learning journals;
- experiment records.

### Knowledge documents

Grow as concepts are learned:

- `CONCEPT_MAP.md`
- `GLOSSARY.md`
- theory notes;
- bibliography.

## 3. Writing style

Prefer concise, technical prose.

For theoretical notes, include when relevant:

1. motivation/problem;
2. notation;
3. assumptions;
4. derivation;
5. interpretation;
6. algorithmic consequences;
7. failure modes;
8. links to related concepts;
9. unresolved questions.

Avoid writing polished explanations that Jonathan has not actually understood yet. Notes should reflect the current level of understanding and can explicitly contain uncertainty.

## 4. Mathematical notation

- Define every symbol at first use.
- Distinguish random variables from realised values where relevant.
- State domains and dimensions when ambiguity matters.
- Do not hide a modelling assumption inside prose.
- When an equation is central, preserve enough derivation that Jonathan can later reconstruct why it is true.

## 5. Experiment identifiers

Use stable identifiers:

- `EXP-001`
- `EXP-002`
- etc.

Do not create folders such as `final_test`, `new_test_2`, or `experiment_good`.

Each experiment should have its own directory under `docs/03_experiments/` and should follow `EXPERIMENT_TEMPLATE.md`.

## 6. Decision identifiers

Use:

- `DEC-001`
- `DEC-002`
- etc.

A changed decision should not disappear. Mark it `SUPERSEDED` and point to the new decision.

## 7. Open-question identifiers

Use:

- `Q-001`
- `Q-002`
- etc.

Questions may be marked:

- OPEN;
- PARTIALLY ANSWERED;
- ANSWERED;
- DEFERRED.

An answer should point to the experiment or theory note that supports it.

## 8. Learning journal

The journal is allowed to be informal. It should capture things such as:

- concepts that finally clicked;
- misconceptions discovered;
- things Jonathan could not reconstruct;
- connections between ideas;
- questions that became interesting;
- techniques that were used too mechanically;
- what should be revisited later.

It is not a daily activity log.

## 9. Session closeout

A session that materially changes understanding or project state should end by checking whether the following require updates:

- `CURRENT_STATE.md`;
- `DECISION_LOG.md`;
- active experiment records;
- theory notes;
- `OPEN_QUESTIONS.md`;
- learning journal;
- bibliography.

Use `docs/99_handoff/SESSION_CLOSEOUT.md`.

## 10. Chat continuity

A future assistant should not need the original chat transcript.

If a new chat cannot determine:

- why the project exists;
- what the learning method is;
- what is currently being studied;
- what has been decided;
- what remains uncertain;
- what should happen next;

then the repository documentation is incomplete and should be fixed.
