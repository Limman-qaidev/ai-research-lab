# EXP-001 — Evolutionary Iterated Prisoner's Dilemma

**Status:** DESIGN  
**Created:** 2026-09-07  
**Last updated:** 2026-09-07

## 1. Research question

Can reciprocal cooperation emerge and persist under population-based evolutionary selection when agents are restricted to stochastic memory-one policies in an Iterated Prisoner's Dilemma, and how do the interaction horizon and evolutionary operators affect the resulting population dynamics?

This question is a design draft and should be ratified before implementation.

## 2. Motivation

EXP-001 is the first application laboratory for the project's bottom-up study of adaptive systems. It is intentionally small enough to understand completely while containing several mechanisms that recur later in reinforcement learning, self-play, multi-agent learning, population methods, and automated discovery:

- state/observation;
- policy representation;
- repeated interaction;
- reward/fitness;
- mutation/exploration;
- selection;
- frequency-dependent performance;
- emergence at population level.

The experiment is not intended to discover a commercially useful strategy. Its purpose is to understand how local behavioural rules and evolutionary pressure generate population-level dynamics.

## 3. Hypotheses

These remain provisional until the formal design gate is closed.

- `H0:` evolutionary selection over stochastic memory-one policies does not produce a reproducible increase or persistence of reciprocal cooperation beyond what is explained by initialization and stochastic drift.
- `H1:` under sufficiently repeated interaction, evolutionary selection can produce and/or maintain policy regions that support reciprocal cooperation and resist unrestricted exploitation.

Secondary hypotheses to consider later:

- longer effective interaction horizons should favour reciprocal strategies relative to one-shot exploitation;
- excessive selection pressure or insufficient mutation may reduce diversity and create premature population concentration;
- mutation scale should create an exploration-versus-local-refinement trade-off.

## 4. System under study

### Environment

Two-player repeated Prisoner's Dilemma.

Stage actions:

`A = {C, D}`

Candidate payoff ordering:

`T > R > P > S`

with the provisional numerical values:

- `T = 5` — temptation payoff, self defects while opponent cooperates;
- `R = 3` — reward for mutual cooperation;
- `P = 1` — punishment for mutual defection;
- `S = 0` — sucker payoff, self cooperates while opponent defects.

The additional condition `2R > T + S` holds for these values and makes sustained mutual cooperation preferable, on average, to alternating exploitation. It does not guarantee cooperation will emerge.

The first implementation must explicitly choose one termination model:

1. fixed finite horizon, or
2. stochastic continuation with probability `delta`.

This choice is not yet ratified.

### Agents / candidates

Each candidate is a stochastic memory-one policy.

With self action written first, the previous-round state is one of:

`CC, CD, DC, DD`.

A complete policy may be represented as:

`pi = (p0, p_CC, p_CD, p_DC, p_DD) in [0,1]^5`

where:

- `p0 = P(C_1)`;
- `p_xy = P(C_t | previous state = xy)` for `t > 1`.

Whether `p0` evolves or is fixed globally remains a design choice.

### Known deterministic reference policies

Under the ordering above:

- `AllC = (1,1,1,1,1)`;
- `AllD = (0,0,0,0,0)`;
- `TFT = (1,1,0,1,0)`.

There are `2^5 = 32` deterministic complete memory-one policies when all five parameters are binary.

### Objective / fitness

Fitness must be derived from game payoff accumulated or averaged across an explicitly defined opponent set.

A key property of the system is that fitness is frequency-dependent: a policy's performance is not an intrinsic scalar independent of population composition.

The exact aggregation rule is not yet ratified.

### Mutation

A candidate mechanism is local additive perturbation:

`pi_child = pi_parent + epsilon`

with parameter bounds enforced so every cooperation probability remains in `[0,1]`.

Clipping is the simplest candidate boundary rule but may create boundary bias. Mutation distribution and scale remain design choices.

### Selection

Selection should favour higher-fitness candidates without assuming that fitness is absolute or stationary.

Binary threshold selection and probabilistic/fitness-weighted selection have been conceptually compared. The exact operator is not yet ratified.

## 5. Experimental variables

### Independent / controlled variables

Candidates for controlled variation:

- effective interaction horizon: fixed rounds or continuation probability `delta`;
- mutation scale;
- selection pressure / selection operator;
- population initialization;
- population size;
- opponent evaluation scheme.

Only a minimal subset should be varied in the first experiment.

### Dependent / measured variables

Candidate metrics:

- population cooperation rate;
- mean and distribution of fitness;
- distribution of the five policy parameters;
- policy diversity / population dispersion;
- prevalence of deterministic or near-boundary strategies;
- pairwise payoff structure among representative policies;
- persistence or collapse of cooperative regimes across generations;
- sensitivity across random seeds.

### Nuisance / confounding factors

- finite-sample noise from stochastic actions;
- random initialization;
- opponent sampling bias;
- mutation boundary handling;
- arbitrary horizon choice;
- selection pressure;
- loss of diversity;
- treating correlated games within a population as independent observations.

## 6. Baselines and controls

At minimum, the first implementation should be able to reproduce known behaviour for:

- AllC vs AllC;
- AllD vs AllC;
- TFT vs AllC;
- TFT vs AllD;
- TFT vs TFT.

These are implementation sanity checks before any evolutionary claim is trusted.

Useful population baselines may later include populations initialized as:

- all AllC plus a rare AllD mutant;
- all TFT plus a rare AllD mutant;
- random stochastic memory-one policies.

## 7. Experimental protocol

Not yet ratified.

Before implementation, choose and justify:

- fixed horizon versus stochastic continuation;
- whether `p0` is evolved;
- population evaluation scheme;
- selection operator;
- mutation distribution and boundary rule;
- population size;
- generations;
- number of seeds;
- stopping rules;
- saved metrics and artefacts.

No numerical values should be copied from earlier discussion merely because they were mentioned.

## 8. Expected failure modes

- claiming emergence when behaviour was effectively encoded by initialization;
- reward/fitness definition accidentally favouring a narrow strategy class;
- insufficient random seeds;
- population collapse caused by selection mechanics rather than game incentives;
- clipping-induced concentration at 0/1 mistaken for evolutionary discovery;
- opponent sampling making fitness comparisons unfair;
- implementation bugs in state ordering (`CD` vs `DC`);
- incorrect first-round handling;
- confusing high aggregate payoff with stable reciprocal cooperation;
- interpreting stochastic drift as adaptation.

## 9. Pre-run predictions

Predictions already supported analytically:

1. In an AllC/AllD population, AllD has higher expected fitness for every AllC population share under the candidate payoffs.
2. TFT vs AllD produces `CD, DD, DD, ...` from TFT's perspective; TFT limits exploitation after the first round.
3. TFT vs TFT sustains `CC` after cooperative initialization.
4. For a fixed `n`-round comparison, TFT-vs-TFT total payoff `3n` exceeds AllD-vs-TFT payoff `n+4` when `n > 2`.
5. Under an uncertain continuation model, increasing continuation probability makes future retaliation/reward more important and should make reciprocal cooperation easier to sustain, all else equal.

No prediction is yet made about the exact evolved policy distribution.

## 10. Implementation notes

**Implementation status:** NOT STARTED.

The next implementation milestone should be deliberately small: Jonathan writes a minimal two-policy repeated-game simulator and tests the deterministic reference matchups before any population evolution code is introduced.

The assistant should provide interface/invariant guidance and code review, not the complete core implementation by default.

## 11. Raw results

None.

## 12. Observations

None.

## 13. Analysis

None.

## 14. Interpretation

None.

## 15. Limitations

The planned experiment is restricted to memory-one policies and therefore cannot represent strategies that require longer histories, explicit opponent models, counts, periodicity detection, learned internal state, or arbitrary planning.

Any cooperation found will be cooperation *within the chosen representation and evolutionary process*, not proof of a universal mechanism for cooperation.

## 16. Conclusion

Pending implementation and results.

## 17. New questions

- Should `p0` be part of the evolved genotype or an environmental initialization convention?
- Which termination model gives the cleanest first experiment while preserving the mechanism we want to study?
- Which selection rule preserves enough diversity without obscuring fitness differences?
- How should mutation boundaries be handled and tested?

## 18. Learning reflection

The foundation phase established the difference among policy, environment, payoff, fitness, mutation, selection, and population dynamics. The next phase should apply these concepts by turning them into explicit experimental design choices and then into Jonathan-authored code.