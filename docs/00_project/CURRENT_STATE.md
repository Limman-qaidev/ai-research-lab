# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0F — EXP-001 first evolutionary trajectory  
**Implementation status:** CORE SIMULATOR, POPULATION FITNESS, SELECTION, MUTATION, SINGLE-GENERATION STEP, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, RANDOM INITIALIZATION AND EXPLICIT NUMPY GENERATOR IMPLEMENTED; ONE-EVALUATION GENERATION CONTRACT FIXED; HISTORY RECORDING STILL IN REVIEW  
**Previous gate:** COMPLETED — `Population.next_generation(fitness, sigma)` now consumes already-computed fitness and no longer re-evaluates the population  
**Next task:** replace the broken history decorators with an explicit per-generation history list, then run the first small exploratory trajectory

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

The simulator and elementary evolutionary system are implemented far enough to perform a first exploratory multi-generation run once history recording is made explicit and correct.

## 4. Validated core simulator and evolutionary operators

A stochastic memory-one `Policy` is represented by `(p0, p_CC, p_CD, p_DC, p_DD)` and uses stochastic action sampling with player-relative previous states.

For a 100-round horizon, deterministic reference matchups were confirmed exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

Population fitness uses one round-robin match per unordered pair:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

Fitness-proportional selection is `q_i = F_i / sum(F)` with replacement.

Mutation is Gaussian and bounded:

`pi_child = clip(pi_parent + epsilon, 0, 1)`, with `epsilon_k ~ Normal(0, sigma^2)`.

The provisional mutation scale remains `sigma = 0.05`.

## 5. Behavioural and policy-space observability

Population evaluation records mean fitness, cooperation/defection rates, and symmetric `CC`, `mixed`, `DD` outcome rates.

The `Population` abstraction also records column-wise mean and standard deviation of its `N x 5` policy matrix.

The deterministic `[TFT, TFT, AllD]` instrumentation check matched the analytical values exactly, including cooperation `0.336666...`, `CC = 1/3`, `mixed = 2/300`, and `DD = 0.66`.

## 6. Random initialization and RNG

`Population(N=...)` creates `N` independent random policies with five parameters sampled uniformly from `[0,1]`.

All current stochastic mechanisms use a NumPy `Generator` created with `np.random.default_rng(42)` for initialization, action sampling, selection, and mutation.

The generator is still module-global; this is acceptable for the first exploratory run. Later multi-seed experiments should make RNG ownership explicit per run.

## 7. One-evaluation generation contract — fixed

Jonathan changed the transition interface to:

`Population.next_generation(fitness, sigma)`

so the method no longer calls `evaluate_population` internally.

The intended generation flow is now explicit:

`fitness_t = population.evaluate(game)`

then use the same `fitness_t` both to record the evaluated generation and to select parents/mutate into `P_(t+1)`.

This satisfies the key invariant:

`fitness recorded in history == fitness used for selection`.

Exactly one stochastic evaluation should occur per generation.

## 8. History decorator attempt — not valid

Jonathan next attempted a generic:

`@history`

decorator on both `Game._get_stats` and `Population.get_stats_population`.

As implemented, the decorator returns an `add` function that merely appends the bound object passed at call time to a private closure list. It therefore replaces the original method body rather than wrapping and executing it.

Consequences:

- `Game._get_stats()` no longer executes its original statistics-collection body;
- `Population.get_stats_population()` no longer computes/returns `(mean, std)` and instead returns `None`;
- the private closure history is inaccessible to the experiment code;
- the attempted `_get_stats` body is itself invalid if restored unchanged because `self.statistics` is a dict with string keys, not positional indices, and `list.append` accepts one object rather than several positional values.

Do not use a decorator for history in v1. History should be explicit experiment state.

## 9. Recommended minimal history design

Create one plain `history = []` owned by the experiment loop.

For each generation `t`:

1. evaluate the current population exactly once and obtain `fitness_t`;
2. read `game.statistics` from that evaluation;
3. compute `mean_pi_t, std_pi_t = population.get_stats_population()`;
4. create one record (preferably a dict) containing `generation`, behavioural statistics, `mean_policy`, and `std_policy`;
5. append that single record to `history`;
6. set `population = population.next_generation(fitness_t, sigma)`.

Do not call `select_parents(fitness_t)` separately for inspection before `next_generation`; that extra random draw consumes the run RNG and changes the subsequent evolutionary trajectory.

## 10. Immediate first-run gate

Before plotting or scaling:

1. remove the `history` decorator/helper and `@history` annotations;
2. restore `Population.get_stats_population()` as an ordinary method returning `(average, std)`;
3. remove `Game._get_stats()` unless there is a clear non-decorator reason to keep it;
4. make the random population checks real assertions, including bounds `[0,1]`;
5. build an explicit `history = []` in the run loop with one dict per generation;
6. run a small exploratory configuration such as `N=30`, `n_rounds=100`, `generations=50`, `sigma=0.05`, seed `42`;
7. inspect the trajectory before any multi-seed scientific claim.

## 11. Resume instruction

Resume at **explicit generation history and first exploratory run**. The double-evaluation issue is fixed. Do not introduce caching or decorators for history. Evaluate each population exactly once, append one explicit history record, reuse the same fitness for reproduction, and then run the first small evolutionary trajectory.