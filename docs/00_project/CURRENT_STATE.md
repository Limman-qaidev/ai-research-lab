# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0E — EXP-001 evolutionary run design and observables  
**Implementation status:** MINIMAL SIMULATOR VALIDATED; POPULATION FITNESS, SELECTION, MUTATION AND SINGLE-GENERATION STEP IMPLEMENTED; BASIC OBSERVABILITY VALIDATED  
**Previous gate:** COMPLETED — population action/outcome instrumentation validated analytically  
**Next task:** add policy-space observables and reproducible random population initialization before the first multi-generation run

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise which mechanisms may address a new problem, explain why they work, reproduce and critique research, adapt methods across domains, and eventually design original experiments, systems, techniques, or hypotheses.

The laboratory is domain-general. Finance may provide future applications, but it is not the organising principle.

`PROJECT_CHARTER.md` v0.2 and `LEARNING_CONTRACT.md` v0.2 were explicitly ratified by Jonathan on 2026-09-06.

## 2. Pedagogical invariant

The expected working sequence remains:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`

The assistant should not default to complete generated implementations for core learning mechanisms. AI should amplify Jonathan's reasoning and programming capability, not replace it.

Socratic questioning should be used only where it tests genuinely important concepts; avoid endless micro-questions. Material progress must be persisted so chat boundaries never force a restart.

## 3. Active experiment — EXP-001

**Working title:** Evolutionary Iterated Prisoner's Dilemma  
**Experiment document:** `docs/03_experiments/EXP-001_EVOLUTIONARY_IPD.md`

The game engine, population evaluator, selection, mutation, and one-generation evolutionary transformation are implemented. Basic behavioural observables have now been instrumented and analytically checked.

## 4. Validated minimal simulator

Jonathan implemented a stochastic memory-one `Policy` with parameters `(p0, p_CC, p_CD, p_DC, p_DD)`, stochastic action sampling, player-relative previous states, Prisoner's Dilemma payoff lookup, and fixed-horizon repeated play.

For a 100-round horizon, deterministic reference matchups were confirmed exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

## 5. Population fitness and evolutionary operators

Population evaluation uses one match for every unordered pair. For population size `N`:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

Fitness-proportional parent sampling is implemented:

`q_i = F_i / sum_j(F_j)`.

Gaussian local mutation is implemented:

`epsilon_k ~ Normal(0, sigma^2)`

`pi_child = clip(pi_parent + epsilon, 0, 1)`.

Each child is a new `Policy`, preserving the previous generation. The provisional experiment mutation scale remains `sigma=0.05`.

The one-generation transformation is implemented as:

`P_t -> evaluate -> fitness -> select parents -> mutate -> P_(t+1)`.

## 6. Basic observability — validated

During population evaluation Jonathan now records:

- total cooperative (`C`) actions;
- total defective (`D`) actions;
- joint outcomes `CC`, `CD`, `DC`, `DD`;
- mean population fitness.

Population-level joint outcomes are reported symmetrically as `CC`, `mixed = CD + DC`, and `DD`.

Correct denominators are now used:

- individual action observations: `n_actions = n_rounds * N * (N-1)`;
- joint round outcomes: `n_joint_rounds = n_rounds * N * (N-1) / 2`.

For `[TFT, TFT, AllD]`, `N=3`, and `n_rounds=100`, the implementation produced:

- `fitness_average = 1.6766666666666667`;
- `cooperation_rate = 0.33666666666666667`;
- `defection_rate = 0.6633333333333333`;
- `CC_rate = 0.3333333333333333`;
- `mixed_rate = 0.006666666666666667`;
- `DD_rate = 0.66`.

These match the analytical counts exactly:

- 600 individual actions: `C=202`, `D=398`;
- 300 joint rounds: `CC=100`, `mixed=2`, `DD=198`.

Validated invariants:

`cooperation_rate + defection_rate = 1`

`CC_rate + mixed_rate + DD_rate = 1`.

For floating-point assertions, prefer tolerance-based checks such as NumPy `isclose` rather than exact equality in future tests.

## 7. Policy-space observables still to add

Before the first evolutionary trajectory, record the mean policy vector per generation:

`mean_pi_t = (mean p0, mean p_CC, mean p_CD, mean p_DC, mean p_DD)`.

This is necessary to distinguish, for example, indiscriminate cooperation from reciprocal cooperation even when aggregate cooperation rates look similar.

A simple dispersion/diversity observable should follow soon after, because the mean alone can hide a heterogeneous population. A candidate is the standard deviation of each policy parameter or a scalar mean distance from the population centroid; do not add unnecessary sophistication in v1.

## 8. Reproducibility status

The current script uses `np.random.seed(42)`. This is sufficient for deterministic implementation checks when the script is run from a fresh process with unchanged random-call order.

Before formal multi-seed experiments, use an explicit run-level NumPy random generator and pass/use it consistently for:

- action sampling;
- parent selection;
- mutation;
- random population initialization.

Record the run seed, population size, rounds per match, generations, and sigma.

## 9. Next gate

Before looping over generations:

1. add the mean policy vector to generation statistics;
2. optionally add a minimal dispersion statistic;
3. initialize a genuinely random population in `[0,1]^5`;
4. replace or encapsulate the global RNG with an explicit run-level generator;
5. define a small exploratory generation count and population size;
6. save one history record per evaluated generation.

Then run the first small evolutionary trajectory and interpret the mechanism before scaling or introducing multi-seed conclusions.

## 10. Resume instruction

Resume at **policy-space observables and random initialization**. Do not revisit Prisoner's Dilemma foundations, deterministic simulator tests, or already validated evolutionary operators unless a regression appears. Basic behavioural instrumentation is now validated. The next objective is to make policy evolution visible and reproducible, then perform the first small multi-generation exploratory run.