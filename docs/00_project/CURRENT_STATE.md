# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0E — EXP-001 evolutionary run design and observables  
**Implementation status:** MINIMAL SIMULATOR VALIDATED; POPULATION FITNESS, SELECTION, MUTATION AND SINGLE-GENERATION STEP IMPLEMENTED; OBSERVABILITY INSTRUMENTATION IN REVIEW  
**Previous gate:** COMPLETED — evaluation, selection and mutation are composed into one next-generation transformation  
**Next task:** correct observable denominators and outcome accounting, then finish reproducible random initialization before the first multi-generation run

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

The game engine and elementary evolutionary operators exist. The project is instrumenting the system so evolutionary trajectories can answer the research question rather than merely produce final fitness numbers.

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

Jonathan has also implemented the complete one-generation transformation:

`P_t -> evaluate -> fitness -> select parents -> mutate -> P_(t+1)`.

## 6. Observable instrumentation — current implementation

Jonathan added counters during population evaluation for:

- total `C` actions;
- total `D` actions;
- observed joint outcomes `CC`, `CD`, `DC`, `DD`;
- mean population fitness.

He also added `np.random.seed(42)` as a first reproducibility mechanism.

### Important denominator correction

The current rate denominators use `num_rounds * N`, which is not the number of observations in a round-robin population when `N > 2`.

Every player plays `N-1` matches of `n` rounds, so the total number of **individual actions** observed in a generation evaluation is:

`n_actions = n_rounds * N * (N - 1)`.

Therefore:

- `cooperation_rate = C_count / n_actions`;
- `defection_rate = D_count / n_actions`;
- invariant: `cooperation_rate + defection_rate = 1`.

The total number of **joint round outcomes** (`CC/CD/DC/DD` as one outcome per played round) is:

`n_joint_rounds = n_rounds * N * (N - 1) / 2`.

Any joint-outcome frequencies must use this denominator, and their rates must sum to 1.

For the deterministic population `[TFT, TFT, AllD]` with `N=3`, `n_rounds=100`:

- there are 3 pairwise matches;
- 300 joint rounds;
- 600 individual actions;
- action counts are `C=202`, `D=398`;
- cooperation rate is `202/600 = 0.336666...`;
- defection rate is `398/600 = 0.663333...`.

These provide an analytical instrumentation test.

### `CD` / `DC` ordering caveat

The current code increments joint state using `state = action_a + action_b` where player A is determined by population index order. Consequently separate aggregate `CD_rate` and `DC_rate` are not permutation-invariant population observables: reordering identical policies in the population can swap these counts.

For population-level reporting, prefer either:

1. symmetric joint categories `CC`, mixed (`CD or DC`), `DD`; or
2. player-relative state counting for both players, if separate `CD` and `DC` frequencies are scientifically needed.

Do not interpret A-oriented `CD` versus `DC` counts as an intrinsic population property.

## 7. Policy-space observables still to add

Before the first evolutionary run, record at least the mean policy vector per generation:

`mean_pi_t = (mean p0, mean p_CC, mean p_CD, mean p_DC, mean p_DD)`.

A dispersion/diversity measure should also be added soon so a stable mean does not hide a heterogeneous population.

Fitness alone cannot establish emergence of reciprocal cooperation.

## 8. Reproducibility status

`np.random.seed(42)` makes a full script reproducible when executed from a fresh process with exactly the same random-call order. This is adequate for immediate implementation checks.

Before scientific multi-seed runs, prefer an explicit NumPy random generator owned by the experiment (for example a generator constructed from a recorded seed) and route action sampling, parent selection, mutation and random initialization through it. This reduces hidden dependence on global RNG state and makes independent runs easier to control.

## 9. Next gate

Before looping over generations:

1. fix action-rate and joint-outcome denominators;
2. validate the `[TFT,TFT,AllD]` instrumentation analytically;
3. choose permutation-invariant joint outcome reporting or player-relative state counting;
4. add the mean policy vector;
5. initialize a genuinely random population in `[0,1]^5`;
6. move from the global seed to explicit run-level RNG before formal multi-seed experiments.

After these are in place, run a small exploratory evolutionary trajectory, record generation history, and inspect mechanisms before scaling.

## 10. Resume instruction

Resume at **observable validation and reproducibility**. Do not revisit Prisoner's Dilemma foundations or the already implemented evolutionary operators unless a regression appears. The immediate task is to make the recorded rates mathematically correct and permutation-safe, then create a reproducible random initial population and perform the first small multi-generation exploratory run.