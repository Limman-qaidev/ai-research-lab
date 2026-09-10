# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0E — EXP-001 evolutionary run design and observables  
**Implementation status:** MINIMAL SIMULATOR VALIDATED; POPULATION FITNESS IMPLEMENTED; FITNESS-PROPORTIONAL SELECTION IMPLEMENTED; MUTATION VALIDATED; SINGLE-GENERATION STEP IMPLEMENTED  
**Previous gate:** COMPLETED — evaluation, selection and mutation are composed into one next-generation transformation  
**Next task:** define generation-level observables and reproducible random initialization before looping over generations

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

The game engine and the elementary evolutionary operators now exist. The project has reached the point where experimental observables and reproducibility must be defined before any long evolutionary run is trusted.

## 4. Validated minimal simulator

Jonathan implemented a stochastic memory-one `Policy` with parameters `(p0, p_CC, p_CD, p_DC, p_DD)`, stochastic action sampling, player-relative previous states, Prisoner's Dilemma payoff lookup, and fixed-horizon repeated play.

For a 100-round horizon, deterministic reference matchups were confirmed exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

## 5. Population fitness

Population evaluation uses one match for every unordered pair. For population size `N`:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

This keeps fitness frequency-dependent on the current population composition.

Analytical targets used during development:

- `[AllC, AllC, AllD]` -> `(1.5, 1.5, 5.0)`;
- `[TFT, TFT, AllD]` -> `(1.995, 1.995, 1.04)`.

## 6. Selection

Fitness-proportional parent sampling is implemented:

`q_i = F_i / sum_j(F_j)`.

Exactly `N` parents are sampled with replacement from the current population.

## 7. Mutation

Gaussian local mutation is implemented:

`epsilon_k ~ Normal(0, sigma^2)`

`pi_child = clip(pi_parent + epsilon, 0, 1)`.

Every child is a new `Policy`, the parent remains unchanged, and child parameters are constrained to `[0,1]`.

The provisional experiment scale remains `sigma=0.05`; larger values used during coding were implementation checks, not ratified experimental choices.

## 8. Single-generation transformation — implemented

Jonathan implemented:

`next_generation(game, population, sigma)`

with the composition:

1. evaluate the current population;
2. sample `N` parents proportionally to fitness;
3. create one independently mutated child for each selected parent;
4. return the resulting `N`-policy population.

This is the first complete evolutionary transition:

`P_t -> fitness -> selection -> mutation -> P_(t+1)`.

### Testing nuance

Calling `select_parents(...)` outside `next_generation(...)` and then calling `next_generation(...)` performs two independent parent selections. Therefore an externally printed `parents[0]` is not necessarily the parent of `next_generation(...)[0]`. To test exact parent-child identity or `sigma=0`, selection and mutation should be observed within the same transition or tested separately.

## 9. Current scientific gate — observables before long runs

Do **not** immediately run hundreds of generations and inspect only final fitness.

The primary EXP-001 question concerns emergence and persistence of reciprocal cooperation. Fitness alone cannot establish that cooperation emerged: exploitative populations can have high individual fitness in some compositions, and aggregate fitness does not identify the behavioural mechanism.

Before the first multi-generation run, define and record at least:

- mean population fitness per generation;
- population cooperation rate per generation (fraction of played actions that are `C`);
- distribution or mean of each policy parameter `(p0, p_CC, p_CD, p_DC, p_DD)`;
- population diversity / dispersion in policy space;
- optional clipping frequency during mutation.

The first implementation may start with a minimal subset, but **cooperation rate is mandatory** if the experiment is to answer its stated research question.

## 10. Reproducibility gate

Before scientific runs:

- initialize a genuinely random population in `[0,1]^5` rather than seeding known strategies;
- control randomness with an explicit seed / NumPy random generator so complete runs can be reproduced;
- record the chosen population size, number of rounds, number of generations, sigma, and seed;
- later repeat across multiple seeds before making claims about emergence.

## 11. Still outstanding

- validate single-generation invariants explicitly if desired (size, bounds, parent immutability, `sigma=0` copy behaviour);
- instrument game/population evaluation to measure cooperation rate without changing the strategic rules;
- define minimal generation-history structure;
- initialize random populations reproducibly;
- choose provisional population size and generation count for a first exploratory run;
- loop over generations and save metrics;
- repeat across seeds before drawing scientific conclusions;
- align minor type annotations and reduce unnecessary hidden `Game` state later.

## 12. Resume instruction

Resume at **experiment observables and reproducibility**, not at game-theory foundations or operator implementation. The single-generation evolutionary transformation already exists. Before running long trajectories, make cooperation observable and create reproducible random initialization. Then perform a small exploratory multi-generation run and analyse the trajectory before scaling up.