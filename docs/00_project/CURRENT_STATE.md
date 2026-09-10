# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0E — EXP-001 evolutionary run design and observables  
**Implementation status:** MINIMAL SIMULATOR VALIDATED; POPULATION FITNESS, SELECTION, MUTATION AND SINGLE-GENERATION STEP IMPLEMENTED; BASIC OBSERVABILITY VALIDATED; POLICY-SPACE MEAN/STD IMPLEMENTED  
**Previous gate:** COMPLETED — behavioural observables validated and policy-space summary statistics implemented  
**Next task:** clean the `Population` generation contract, add reproducible random population initialization, then perform the first small multi-generation run

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

The game engine, population evaluator, selection, mutation, one-generation evolutionary transformation, behavioural observables, and first policy-space observables are implemented.

## 4. Validated core simulator

Jonathan implemented a stochastic memory-one `Policy` with parameters `(p0, p_CC, p_CD, p_DC, p_DD)`, stochastic action sampling, player-relative previous states, Prisoner's Dilemma payoff lookup, and fixed-horizon repeated play.

For a 100-round horizon, deterministic reference matchups were confirmed exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

Population fitness is evaluated over one match per unordered pair:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

Fitness-proportional selection and Gaussian mutation are implemented, with mutation

`pi_child = clip(pi_parent + epsilon, 0, 1)`, `epsilon_k ~ Normal(0, sigma^2)`.

The one-generation transformation exists:

`P_t -> evaluate -> fitness -> select parents -> mutate -> P_(t+1)`.

## 5. Behavioural observability — validated

During population evaluation the simulator records:

- mean fitness;
- cooperation and defection rates;
- symmetric joint-outcome rates `CC`, `mixed = CD + DC`, and `DD`.

For `[TFT, TFT, AllD]`, `N=3`, `n_rounds=100`, the implementation produced exactly the analytically expected values:

- `fitness_average = 1.6766666666666667`;
- `cooperation_rate = 0.33666666666666667`;
- `defection_rate = 0.6633333333333333`;
- `CC_rate = 0.3333333333333333`;
- `mixed_rate = 0.006666666666666667`;
- `DD_rate = 0.66`.

Validated invariants:

`cooperation_rate + defection_rate = 1`

`CC_rate + mixed_rate + DD_rate = 1`.

Use tolerance-based floating-point checks such as `np.isclose` in tests.

## 6. Policy-space observables — implemented

Jonathan introduced a `Population` abstraction containing its list of `Policy` objects and implemented population parameter summaries by assembling an `N x 5` policy matrix and computing column-wise:

- mean policy vector;
- standard deviation vector.

For the test population `[TFT, TFT, AllD]`, the expected summaries are:

`mean_pi = (2/3, 2/3, 0, 2/3, 0)`

`std_pi = (sqrt(2)/3, sqrt(2)/3, 0, sqrt(2)/3, 0)` approximately `(0.4714, 0.4714, 0, 0.4714, 0)`.

These observables will help distinguish behavioural cooperation from the policy mechanism producing it.

### Small implementation cleanups before repeated generations

The current code uses `np.matrix`; prefer a normal NumPy `ndarray` (e.g. from `np.array`/`np.stack`) because `np.matrix` has special two-dimensional semantics and is unnecessary here.

The helper is named `__get_matrix__`; it is not a Python special method, so a single-underscore private helper name is clearer.

More importantly, `Population.next_generation(self, game, population, sigma)` currently mixes an externally supplied `population` for evaluation with `self.policies` for parent selection. This creates two possible population sources. Before a multi-generation loop, make the contract internally consistent: a `Population` method should operate on its own `self.policies`, and the returned next generation should have a clear representation (preferably another `Population` if continuing with this abstraction).

## 7. Reproducibility status

The current script uses `np.random.seed(42)`, which is adequate for implementation checks in a fresh process with unchanged random-call order.

Before scientific runs, use an explicit run-level NumPy random generator and route all stochastic mechanisms through it:

- random initial policy generation;
- action sampling;
- parent selection;
- mutation.

Record the seed, population size, rounds per match, generation count, and sigma for every run.

## 8. Immediate next gate

Do not yet scale to long runs.

1. make the `Population.next_generation` contract self-consistent;
2. use an `ndarray` rather than `np.matrix` for policy summaries;
3. create a genuinely random initial population with five independent parameters in `[0,1]` per policy;
4. move from global RNG state to an explicit run-level generator;
5. save one history record per evaluated generation containing at least fitness, cooperation, joint outcomes, mean policy vector, and standard-deviation vector;
6. run a small exploratory trajectory and interpret it before multi-seed experiments.

## 9. Resume instruction

Resume at **random initialization and clean population iteration**. Do not revisit Prisoner's Dilemma foundations or already validated operators unless a regression appears. Policy-space mean/std now exist. The next objective is to make `Population -> Population` evolution unambiguous and reproducible, initialize `P_0` randomly, and then execute the first small evolutionary trajectory.