# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0F — EXP-001 first evolutionary trajectory  
**Implementation status:** CORE SIMULATOR, POPULATION FITNESS, SELECTION, MUTATION, SINGLE-GENERATION STEP, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, RANDOM INITIALIZATION AND EXPLICIT NUMPY GENERATOR IMPLEMENTED; ONE-EVALUATION GENERATION CONTRACT FIXED; EXPERIMENT HISTORY LOOP STILL MISALIGNED  
**Previous gate:** COMPLETED — `Population.next_generation(fitness, sigma)` consumes already-computed fitness and no longer re-evaluates the population  
**Next task:** simplify `experiment()` so each generation evaluates exactly once and records behavioural statistics and policy statistics from the same population, then run the first exploratory trajectory

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

The simulator and evolutionary operators are implemented. The remaining blocker before the first exploratory trajectory is correct alignment of generation history.

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

The `Population` abstraction reports column-wise mean and standard deviation of its `N x 5` policy matrix.

The deterministic `[TFT, TFT, AllD]` instrumentation check matched analytical values exactly.

## 6. Random initialization and RNG

`Population(N=...)` creates `N` independent random policies with five parameters sampled uniformly from `[0,1]`.

All current stochastic mechanisms use a NumPy `Generator` created with `np.random.default_rng(42)` for initialization, action sampling, selection, and mutation.

The generator is still module-global; this is acceptable for the first exploratory run. Later multi-seed experiments should make RNG ownership explicit per run.

## 7. One-evaluation generation contract — fixed

`Population.next_generation(fitness, sigma)` no longer evaluates internally.

The intended invariant remains:

`fitness recorded in history == fitness used for selection`.

Exactly one stochastic evaluation should occur per generation.

## 8. Current experiment-loop bug

Jonathan removed the broken decorator and introduced an explicit `experiment()` loop, but the current structure still evaluates populations twice and misaligns behavioural and policy-space statistics.

Current sequence is effectively:

1. evaluate `P0` before the loop;
2. generation 0 records those statistics;
3. evaluate `P0` again;
4. generate `P1` from the second `P0` fitness;
5. compute mean/std of `P1`;
6. generation 1 begins while `game.statistics` still refers to the second evaluation of `P0`.

Therefore from generation 1 onward, a record can combine behavioural statistics from `P_(t-1)` with mean/std from `P_t`.

There is also unnecessary duplicate history state inside `Game`:

- `Game.history` is maintained separately from the experiment-level `history`;
- `evaluate_population()` calls `self._get_stats()` automatically;
- `experiment()` calls `game._get_stats()` again, duplicating entries;
- `_get_stats()` returns the entire cumulative `Game.history`, so `record.extend(game._get_stats())` extends the record with nested historical rows rather than with the current scalar statistics.

## 9. Correct minimal generation flow

History should have a single owner: `experiment()`.

For each generation `t`, perform exactly:

1. `fitness_t = population.evaluate(game)`;
2. read/copy `game.statistics` from that same evaluation;
3. compute `mean_pi_t, std_pi_t = population.get_stats_population()` from that same `population`;
4. create one history record containing generation index, behavioural statistics, mean policy and std policy;
5. append the record once;
6. set `population = population.next_generation(fitness_t, sigma)`.

There should be no pre-loop evaluation and no `Game.history` / `_get_stats()` mechanism in v1.

Prefer one dict per generation rather than a flattened positional list because names preserve the meaning of each observable and reduce index/order mistakes.

## 10. Immediate first-run gate

1. remove `Game.history` and `_get_stats()` from the history path;
2. remove the pre-loop evaluation in `experiment()`;
3. evaluate once at the top of each generation iteration;
4. record behavioural stats and policy mean/std before replacing the population;
5. reuse that same `fitness_t` in `next_generation`;
6. validate random-population invariants including parameter bounds `[0,1]`;
7. run the first exploratory configuration, preferably `N=30`, `n_rounds=100`, `generations=50`, `sigma=0.05`, seed `42`;
8. inspect the trajectory before plotting or making multi-seed claims.

## 11. Resume instruction

Resume at the **minimal experiment loop**. The evolutionary operators are complete. Do not add decorators, caches, or a second history store. Each generation must be evaluated exactly once, its behavioural and policy statistics recorded together, and the same fitness reused for reproduction. Once this alignment is correct, run the first exploratory trajectory.