# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0G — EXP-001 exploratory evolutionary dynamics  
**Implementation status:** CORE SIMULATOR, POPULATION FITNESS, SELECTION, MUTATION, GENERATION TRANSITION, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, RANDOM INITIALIZATION, EXPLICIT NUMPY GENERATOR AND ALIGNED EXPERIMENT HISTORY IMPLEMENTED; FIRST EXPLORATORY TRAJECTORY COMPLETED  
**Previous gate:** COMPLETED — each generation is evaluated exactly once and the same fitness is reused for history and reproduction  
**Next task:** remove redundant `Game.history`, add an explicit reciprocity/conditional-response observable, then run a longer exploratory trajectory before any multi-seed claim

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

The repeated-game simulator and first complete population-evolution experiment loop are now operational. The project has produced its first multi-generation exploratory trajectory from a genuinely random stochastic memory-one population.

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

Population evaluation records:

- mean fitness;
- cooperation and defection rates;
- symmetric joint-outcome rates `CC`, `mixed = CD + DC`, and `DD`.

The `Population` abstraction reports the column-wise mean and standard deviation of its `N x 5` policy matrix.

The deterministic `[TFT, TFT, AllD]` instrumentation check matched analytical values exactly.

## 6. Random initialization and RNG

`Population(N=...)` creates `N` independent random policies with five parameters sampled uniformly from `[0,1]`.

All current stochastic mechanisms use a NumPy `Generator` created with `np.random.default_rng(42)` for initialization, action sampling, selection, and mutation.

The generator is still module-global. This is acceptable for the current exploratory stage, but multi-seed experiments should make the RNG an explicit run-owned dependency.

## 7. Generation-history contract — fixed

The experiment loop now performs exactly one stochastic evaluation per generation:

1. `fitness_t = population.evaluate(game)`;
2. read `game.statistics` from that evaluation;
3. compute mean/std from that same `population`;
4. append one explicit dict record to experiment-level `history`;
5. produce `P_(t+1)` using `population.next_generation(fitness_t, sigma)`.

Therefore:

`fitness recorded in history == fitness used for selection`.

Behavioural statistics and policy-space statistics in each history row refer to the same `P_t`.

## 8. First exploratory trajectory — completed

Jonathan ran an initial trajectory with:

- population size `N = 100`;
- fixed match horizon `n_rounds = 100`;
- generations `G = 10`;
- mutation scale `sigma = 0.05`;
- RNG seed `42`.

This is an exploratory implementation/science check, not a basis for general conclusions.

Observed generation 0 -> generation 9 changes included:

- mean fitness: `2.2228 -> 1.8075`;
- cooperation rate: `0.4868 -> 0.3040`;
- `CC_rate`: `0.2376 -> 0.1045`;
- `mixed_rate`: `0.4985 -> 0.3990`;
- `DD_rate`: `0.2640 -> 0.4965`.

Thus aggregate cooperation decreased substantially during this short run while mutual defection increased.

Mean policy vector changed from approximately:

`(0.5277, 0.5122, 0.5152, 0.4308, 0.4925)`

to:

`(0.5594, 0.4718, 0.3185, 0.4031, 0.2617)`.

The strongest directional changes were decreases in `p_CD` and `p_DD`, while `p_CC` and `p_DC` decreased much less.

## 9. Preliminary reciprocity-like signal

For a self-first memory-one state representation, the opponent cooperated previously in states `CC` and `DC`, and defected previously in states `CD` and `DD`.

A simple conditional-response / reciprocity index is therefore:

`R = ((p_CC + p_DC) - (p_CD + p_DD)) / 2`.

Interpretation:

- `R > 0`: greater willingness to cooperate after opponent cooperation than after opponent defection;
- `R = 0`: no average discrimination based on opponent's previous action;
- `R < 0`: reverse conditionality.

Using the population mean parameters from the first run:

- generation 0: `R ≈ -0.0323`;
- generation 9: `R ≈ +0.1473`.

Across the ten recorded generations this index was approximately:

`[-0.0323, 0.0036, -0.0202, 0.0515, 0.0896, 0.1239, 0.0756, 0.0921, 0.1458, 0.1473]`.

This is an interesting exploratory signal: aggregate cooperation fell, but the mean policy became more conditionally responsive to whether the opponent had cooperated or defected. Do **not** yet call this emergence of reciprocal cooperation. The population remains heterogeneous, the run is short, and only one seed has been observed. The signal should become an explicit observable and then be tested over longer and multi-seed runs.

## 10. Remaining cleanup

`Game` still contains an internal `history` list and `_get_stats()` that is automatically called by `evaluate_population()`. This no longer causes the double-evaluation bug and does not affect the RNG, but it is redundant because experiment-level `history` is now the authoritative trajectory record. Remove it to avoid two history stores and future confusion.

Other non-blocking cleanup:

- make `experiment()` either require explicit `population`/`game` arguments or restore safe creation when they are `None`;
- lower the default generation count from `1000` for exploratory use or require it explicitly;
- align `Policy.__init__` typing with accepted array-like inputs;
- eventually make RNG ownership explicit per experimental run;
- later record clipping frequency during mutation.

## 11. Immediate next gate

1. remove redundant `Game.history` / `_get_stats()`;
2. add the reciprocity index `R` to each generation history row;
3. keep cooperation rate and mean/std policy observables;
4. run a longer exploratory trajectory, e.g. `N=30`, `n_rounds=100`, `G=50`, `sigma=0.05`, seed `42`, or retain `N=100` if runtime is acceptable;
5. inspect whether reciprocity-like conditionality persists, collapses, or coexists with low aggregate cooperation;
6. only after understanding one longer trajectory move to multiple independent seeds.

## 12. Resume instruction

Resume at **analysis of the first evolutionary dynamics and explicit reciprocity observability**. The experiment loop is now structurally correct. Do not revisit basic game theory or evolutionary operators unless a regression appears. The next scientific question is not merely whether cooperation rises, but whether selection is producing stable conditional/reciprocal response structure and under what conditions.