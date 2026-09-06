# Experiments

Every substantive empirical investigation receives a permanent identifier:

`EXP-001`, `EXP-002`, ...

Do not use temporary names such as `final_test`, `new_experiment`, or `experiment_good`.

## Experiment lifecycle

A typical experiment moves through:

`IDEA -> DESIGN -> IMPLEMENTATION -> RUNNING -> ANALYSIS -> CLOSED`

An experiment may also end as `ABANDONED` or `INCONCLUSIVE`. Negative or null results are still project knowledge.

## Required separation

Do not mix these concepts:

- **Question:** what are we trying to learn?
- **Hypothesis:** what do we expect before seeing results?
- **Design:** how will the question be tested?
- **Observation:** what did the data show?
- **Interpretation:** what do we think explains it?
- **Conclusion:** what is actually supported?

## Reproducibility

Where relevant, record:

- code/commit reference;
- random seeds;
- configuration;
- environment/package versions;
- hardware if it can affect results;
- raw-result location;
- analysis procedure.

## First planned experiment

The first experiment will be:

`EXP-001 — Evolutionary Iterated Prisoner’s Dilemma`

Its design has not yet been formally opened because the project charter and learning contract must first be ratified.

Use `EXPERIMENT_TEMPLATE.md` when the experiment is created.
