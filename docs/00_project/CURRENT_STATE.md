# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0F — EXP-001 first evolutionary trajectory  
**Implementation status:** CORE SIMULATOR, POPULATION FITNESS, SELECTION, MUTATION, SINGLE-GENERATION STEP, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, RANDOM INITIALIZATION AND EXPLICIT NUMPY GENERATOR IMPLEMENTED  
**Previous gate:** COMPLETED — random `Population(N)` initialization and `Population -> Population` generation contract implemented  
**Next task:** implement generation history with exactly one stochastic evaluation per generation, then run the first small exploratory trajectory

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

The repeated-game engine and elementary evolutionary system are implemented far enough to perform a first exploratory multi-generation run once history recording is correct.

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

`Population(N=...)` creates `N` independent policies, each with five parameters sampled uniformly from `[0,1]`.

The policy matrix uses `np.array(...)` and has intended shape `(N, 5)`.

Current stochastic mechanisms use a NumPy `Generator` created with:

`rng = np.random.default_rng(42)`.

The generator is used for policy initialization, policy action sampling, parent selection, and mutation. This is sufficient for the first exploratory run in a fresh process. A later refinement should make the RNG an explicit run-owned dependency rather than a module-global object, especially for independent multi-seed experiments.

## 7. Population generation contract

`Population.next_generation(game, sigma)` currently performs:

`evaluate self.policies -> select parents -> mutate -> return new Population`.

This is conceptually correct for one isolated generation, but history recording must not trigger another evaluation of the same population.

## 8. Critical generation-history invariant

Exactly **one stochastic evaluation per generation** must be performed and reused for both:

- the statistics recorded in history;
- the fitness used for parent selection.

The attempted use of a custom `@cache` decorator on `Game.evaluate_population` is not appropriate and, as written, breaks the method: the decorator returns `(add, get)`, so the decorated method is replaced by a tuple rather than remaining callable.

Do not solve this with memoization. The clean design is explicit data flow:

`fitness_t = population.evaluate(game)`

then use the same `fitness_t` to:

1. record `game.statistics` plus the population mean/std;
2. create the next generation via selection and mutation without re-evaluating.

A clean interface is therefore for the generation-transition method to accept the already-computed `fitness`, or to split reproduction from evaluation entirely. Avoid hidden cached stochastic results because they make invalidation and population identity harder to reason about.

## 9. Immediate first-run gate

Before plotting or scaling:

1. remove the broken cache decorator/helper;
2. ensure the random population checks are real assertions (`len == N`, shape `(N,5)`, bounds `[0,1]`);
3. change the generation transition so it consumes already-computed fitness and does not call `evaluate_population` internally;
4. save one history record per generation containing generation index, mean fitness, cooperation rate, `CC/mixed/DD`, mean policy vector, and policy std vector;
5. run a small exploratory configuration such as `N=30`, `n_rounds=100`, `generations=50`, `sigma=0.05`, seed `42`;
6. inspect the trajectory before any multi-seed scientific claim.

## 10. Small cleanup items

- `np.isclose(...)` used as a bare expression does not test anything; wrap it in `assert` when intended as validation.
- `len(population.policies) == 100` and `population._get_matrix().shape == (100,5)` are also discarded booleans unless wrapped in `assert`.
- `Policy.__init__` is annotated with `np.ndarray` but some examples pass Python lists; align the contract later.
- `Game.evaluate_population` can use the value returned by `play_game` rather than reading `self.total_score`; this is cleanup, not a scientific blocker.

## 11. Resume instruction

Resume at **generation history and first exploratory run**. Remove the attempted cache. Evaluate each population exactly once, reuse that fitness for history and reproduction, then run the first small evolutionary trajectory. Do not revisit Prisoner's Dilemma foundations or already validated operators unless a regression appears.