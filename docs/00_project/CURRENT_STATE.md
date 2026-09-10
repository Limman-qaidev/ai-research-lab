# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0F — EXP-001 first evolutionary trajectory  
**Implementation status:** CORE SIMULATOR, POPULATION FITNESS, SELECTION, MUTATION, SINGLE-GENERATION STEP, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, RANDOM INITIALIZATION AND EXPLICIT NUMPY GENERATOR IMPLEMENTED  
**Previous gate:** COMPLETED — random `Population(N)` initialization and `Population -> Population` generation contract implemented  
**Next task:** implement generation history without double-evaluating populations, then run the first small exploratory trajectory

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise which mechanisms may address a new problem, explain why they work, reproduce and critique research, adapt methods across domains, and eventually design original experiments, systems, techniques, or hypotheses.

The laboratory is domain-general. Finance may provide future applications, but it is not the organising principle.

`PROJECT_CHARTER.md` v0.2 and `LEARNING_CONTRACT.md` v0.2 were explicitly ratified by Jonathan on 2026-09-06.

## 2. Pedagogical invariant

The expected working sequence remains:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`

The assistant should not default to complete generated implementations for core learning mechanisms. AI should amplify Jonathan's reasoning and programming capability, not replace it.

Use Socratic questions only for important conceptual distinctions; avoid endless micro-questions and keep application progress visible.

## 3. Active experiment — EXP-001

**Working title:** Evolutionary Iterated Prisoner's Dilemma  
**Experiment document:** `docs/03_experiments/EXP-001_EVOLUTIONARY_IPD.md`

The repeated-game engine and elementary evolutionary system are now implemented far enough to perform a first exploratory multi-generation run once history recording is correct.

## 4. Validated core simulator

A stochastic memory-one `Policy` is represented by `(p0, p_CC, p_CD, p_DC, p_DD)` and uses stochastic action sampling with player-relative previous states.

For a 100-round horizon, deterministic reference matchups were confirmed exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

Population fitness is one round-robin match per unordered pair:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

Fitness-proportional selection is `q_i = F_i / sum(F)` with replacement.

Mutation is Gaussian and bounded:

`pi_child = clip(pi_parent + epsilon, 0, 1)`, with `epsilon_k ~ Normal(0, sigma^2)`.

The provisional mutation scale remains `sigma = 0.05`.

## 5. Behavioural and policy-space observability

Population evaluation records:

- mean fitness;
- cooperation and defection rates;
- symmetric joint-outcome rates `CC`, `mixed = CD + DC`, and `DD`.

For `[TFT, TFT, AllD]`, `N=3`, `n_rounds=100`, observed values matched the analytical result exactly:

- mean fitness `1.6766666666666667`;
- cooperation `0.33666666666666667`;
- defection `0.6633333333333333`;
- `CC = 0.3333333333333333`;
- `mixed = 0.006666666666666667`;
- `DD = 0.66`.

The `Population` abstraction also builds an `N x 5` ndarray and reports column-wise mean and standard deviation of policy parameters.

For `[TFT, TFT, AllD]`:

`mean_pi = (2/3, 2/3, 0, 2/3, 0)`

`std_pi ≈ (0.4714, 0.4714, 0, 0.4714, 0)`.

## 6. Random initialization and RNG — implemented

`Population(N=...)` now creates `N` independent policies, each with five parameters sampled uniformly from `[0,1]`.

The policy matrix uses `np.array(...)` and therefore has intended shape `(N, 5)`.

The previous global `np.random.seed(...)` / module-level random calls were replaced by a NumPy `Generator` created with:

`rng = np.random.default_rng(42)`.

Current stochastic mechanisms use this generator for:

- policy initialization;
- policy action sampling;
- parent selection;
- mutation.

This is sufficient for the first exploratory run in a fresh process. A later refinement should make the RNG an explicit run-owned dependency rather than a module-global object, especially for independent multi-seed experiments.

## 7. Population generation contract — cleaned

`Population.next_generation(game, sigma)` now evaluates `self.policies`, selects parents from that same population, mutates them, and returns a new `Population`.

This removes the earlier ambiguity where an externally supplied population could be evaluated while parents were selected from `self.policies`.

Current conceptual transition:

`P_t -> evaluate P_t -> fitness_t -> select parents from P_t -> mutate -> P_(t+1)`.

## 8. Critical issue before the multi-generation loop: do not evaluate twice

The next step is to record one history row per generation. Be careful not to do both:

1. `population.evaluate(game)` to obtain statistics for history; and then
2. `population.next_generation(game, sigma)`, which currently evaluates the same population again internally.

Because policy actions are stochastic, those are two different evaluations and consume different RNG draws. Selection would then use a different fitness realization from the one recorded in history, and the extra evaluation would change the complete future random trajectory.

Before looping, choose one evaluation per generation and reuse that same fitness for both recorded statistics and parent selection.

A clean conceptual generation step is:

`evaluate once -> record statistics/mean/std -> select using that fitness -> mutate -> next population`.

The exact interface is for Jonathan to implement; avoid providing a full core implementation unless requested.

## 9. Small cleanup items

- `np.isclose(...)` used as a bare expression does not test anything; wrap it in `assert` when intended as a validation.
- `Policy.__init__` is annotated with `np.ndarray` but development examples sometimes pass Python lists; align the contract later.
- `Game.evaluate_population` can use the value returned by `play_game` rather than reading `self.total_score`; this is cleanup, not a scientific blocker.
- A module-global `rng` is better than the legacy global NumPy random API but is not yet fully isolated run-level RNG ownership.

## 10. Immediate first-run gate

Before scaling or plotting:

1. validate a random `Population(N)` has `N` policies, policy matrix shape `(N,5)`, and all entries in `[0,1]`;
2. restructure generation execution so each population is evaluated exactly once;
3. save one history record from that evaluation containing at least generation index, mean fitness, cooperation rate, `CC/mixed/DD`, mean policy vector, and policy std vector;
4. use a small exploratory configuration such as `N=30`, `n_rounds=100`, `generations=50`, `sigma=0.05`, seed `42`;
5. inspect the trajectory before any multi-seed scientific claim.

## 11. Resume instruction

Resume at **generation history and first exploratory run**. Do not revisit Prisoner's Dilemma foundations or already validated evolutionary operators unless a regression appears. Random initialization and a NumPy Generator are now in place. The key next invariant is one stochastic population evaluation per generation, reused for both history and selection.