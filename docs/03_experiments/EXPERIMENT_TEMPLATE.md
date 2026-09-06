# EXPERIMENT_TEMPLATE.md

# EXP-XXX — Title

**Status:** IDEA | DESIGN | IMPLEMENTATION | RUNNING | ANALYSIS | CLOSED | INCONCLUSIVE | ABANDONED  
**Created:** YYYY-MM-DD  
**Last updated:** YYYY-MM-DD

## 1. Research question

What exactly are we trying to learn?

## 2. Motivation

Why is this question important at the current point in the roadmap?

What prior concepts does it depend on?

## 3. Hypotheses

State hypotheses **before** inspecting the results.

Example structure:

- `H0:` ...
- `H1:` ...

If the experiment is exploratory, say so explicitly instead of inventing a retrospective hypothesis.

## 4. System under study

Define:

- environment;
- agents/candidates;
- state or observation space;
- action space;
- policy/representation;
- interaction rules;
- objective/reward/fitness;
- stochastic components.

Include mathematical notation where useful.

## 5. Experimental variables

### Independent / controlled variables

- ...

### Dependent / measured variables

- ...

### Nuisance/confounding factors

- ...

## 6. Baselines and controls

What simpler or known methods must the system be compared against?

## 7. Experimental protocol

Specify:

- number of runs;
- random seeds;
- episode/game/generation count;
- evaluation protocol;
- train/evaluation separation if relevant;
- stopping rules;
- statistical summaries;
- saved artefacts.

Do not choose numbers only because they were mentioned in an earlier chat. Justify values that materially affect conclusions.

## 8. Expected failure modes

Before running, list ways the experiment could mislead us.

Examples:

- evaluator/reward misspecification;
- insufficient seeds;
- selection bias;
- overfitting to opponents or instances;
- non-independent observations;
- hidden leakage;
- misleading aggregate metrics;
- implementation bugs that mimic learning.

## 9. Pre-run predictions

Record qualitative or quantitative predictions before seeing data.

This makes later surprise scientifically useful.

## 10. Implementation notes

Reference the code modules and tests, but do not duplicate implementation documentation here.

**Code commit:**  
**Configuration:**  
**Environment:**

## 11. Raw results

Record paths/artefacts only. Do not interpret yet.

## 12. Observations

What happened, stated as descriptively as possible?

Separate observations from explanations.

## 13. Analysis

Include statistical summaries, plots, robustness checks, sensitivity analyses, and comparisons with baselines.

## 14. Interpretation

What mechanisms might explain the observations?

Which interpretations are supported, plausible, or speculative?

## 15. Limitations

What can this experiment **not** establish?

## 16. Conclusion

Answer the original research question using only the evidence obtained.

## 17. New questions

Add durable questions to `docs/05_questions/OPEN_QUESTIONS.md`.

## 18. Learning reflection

What did Jonathan understand better as a result of designing, implementing, or analysing this experiment?

If useful, also update the learning journal.
