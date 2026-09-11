# CURRENT_STATE.md

**Last updated:** 2026-09-11  
**Project phase:** Stage 0H — EXP-001 multi-seed replication preparation  
**Implementation status:** CORE SIMULATOR, FITNESS, SELECTION, MUTATION, RANDOM INITIALIZATION, RUN-OWNED RNG, ALIGNED HISTORY, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, CONDITIONAL-RESPONSE OBSERVABLES AND FIRST PERFORMANCE CLEANUPS IMPLEMENTED  
**Previous gate:** COMPLETED — reproducibility and independent-seed plumbing validated  
**Next task:** run the first 50-generation multi-seed replication and persist run configurations/results in a stable format

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise mechanisms, derive and reproduce them, critique experiments, and eventually design original techniques, systems, or hypotheses.

Pedagogical sequence:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`.

## 2. Active experiment

**EXP-001 — Evolutionary Iterated Prisoner's Dilemma**  
Detailed experiment record: `docs/03_experiments/EXP-001_EVOLUTIONARY_IPD.md`.

Implemented memory-one policy:

`pi = (p0, p_CC, p_CD, p_DC, p_DD)`.

Self action is written first in `CC, CD, DC, DD`.

## 3. Implemented v1 design

- payoffs: `T=5, R=3, P=1, S=0`;
- fixed horizon: `n_rounds=100`;
- full round-robin evaluation, one match per unordered pair;
- fitness: `F_i = total_payoff_i / ((N-1)*n_rounds)`;
- fitness-proportional parent selection with replacement;
- Gaussian mutation on all five policy components;
- clipping to `[0,1]`;
- provisional `sigma=0.05`;
- initial policies sampled independently from `Uniform(0,1)^5`;
- exactly one stochastic population evaluation per generation;
- same fitness vector is recorded and reused for reproduction.

## 4. Validated simulator baselines

For 100-round deterministic matches:

- AllC vs AllC -> `(300,300)`;
- AllD vs AllC -> `(500,0)`;
- AllD vs AllD -> `(100,100)`;
- TFT vs AllD -> `(99,104)`;
- TFT vs TFT -> `(300,300)`;
- TFT vs AllC -> `(300,300)`.

Population instrumentation was also validated analytically on `[TFT,TFT,AllD]`.

## 5. Recorded observables

Per generation:

- mean fitness;
- cooperation / defection rate;
- `CC`, `mixed=CD+DC`, `DD` rates;
- mean policy vector;
- policy-parameter standard deviations;
- `R_opp = ((p_CC+p_DC) - (p_CD+p_DD))/2`;
- `R_self = ((p_CC+p_CD) - (p_DC+p_DD))/2`.

`R_opp` measures average response to the opponent's previous action. `R_self` measures persistence of the agent's own previous action. Both are computed from the mean policy vector; because they are linear, this equals averaging the individual indices.

## 6. Seed-42 50-generation baseline

Configuration: `N=30`, `G=50`, `n_rounds=100`, `sigma=0.05`, seed `42`.

Generation 0 -> 49:

- mean fitness `2.1965 -> 1.4174`;
- cooperation `0.4734 -> 0.1518`;
- `DD_rate 0.2769 -> 0.7344`;
- mean policy changes from approximately `(0.489,0.533,0.501,0.436,0.467)` to `(0.269,0.568,0.406,0.233,0.090)`;
- `R_opp 0.0009 -> 0.1526`;
- `R_self 0.0659 -> 0.3261`.

The trajectory contains a regime change rather than a smooth move to AllD or TFT. Around generation 19 the mean policy has high `p_DD` and low `p_DC`, producing anti-persistent/alternating structure. By generation 49, low `p0` and very low `p_DD` create a strong defection trap: many matches enter `DD` early and then remain there.

The late population is not TFT-like. Own-action persistence is stronger than opponent responsiveness.

All of this remains a single-seed exploratory observation until replication.

## 7. Run-owned RNG — validated

The module-global RNG has been removed.

`experiment(seed, ...)` now creates a fresh `np.random.default_rng(seed)` and explicitly passes that same run-owned generator through every stochastic mechanism:

- initial population generation;
- stochastic policy actions;
- parent selection;
- Gaussian mutation.

`Policy` and `Population` do not hide RNG state as object attributes.

Reproducibility test completed:

- run A, seed `42`;
- run B, seed `42`;
- run C, seed `43`.

A and B were identical generation-by-generation, while C differed already at generation 0. Therefore the experiment now satisfies the intended computational invariant:

`same code + same configuration + same seed -> same trajectory`.

## 8. Multi-seed runner — plumbing validated

`experiment()` now returns run configuration separately from generation history.

Current run configuration contains:

- seed;
- generation count (`game_number`, pending rename to `generations`);
- population size (`players`, pending rename to `population_size`);
- number of rounds;
- mutation sigma.

A five-run smoke test was executed with seeds `42,43,44,45,46`, `N=30`, `G=5`, `n_rounds=100`, `sigma=0.05`.

The runner successfully produced distinct histories while preserving each run's configuration. The short trajectories already show substantial stochastic variation: for example seed 43 moved toward lower cooperation over five generations, while seed 44 moved toward higher cooperation. This is a plumbing/variability check only, not a scientific conclusion because `G=5` is too short.

## 9. Runtime / implementation status

Safe performance cleanups implemented:

- Bernoulli action draw via `rng.random() < p`;
- payoff table created once in `Game.__init__`;
- joint state constructed directly as `action_a + action_b`;
- redundant internal `Game.history/_get_stats()` removed;
- run arguments are explicit.

Runtime still scales approximately as:

`G * N(N-1)/2 * n_rounds`.

## 10. Remaining cleanup

Non-blocking code-contract cleanup:

- rename `game_number` -> `generations`;
- rename `players` -> `population_size`;
- type `Population.policies` as `list[Policy] | None` because `None` triggers random initialization;
- eventually simplify the `Population` constructor because `N` and `rng` are irrelevant when an explicit policy list is provided.

Methodological work:

- persist configuration and history in a stable serializable format; NumPy arrays/scalars need conversion before JSON serialization;
- add an interaction term for the 2x2 memory-one response table only if scientifically useful;
- record mutation clipping frequency later;
- quantify stochastic fitness noise later via repeated matches, longer horizons, or uncertainty analysis.

## 11. Immediate next gate

1. keep `N=30`, `n_rounds=100`, `sigma=0.05` fixed;
2. run at least five independent seeds for `G=50` as the first replication batch;
3. preserve each run's configuration and complete trajectory;
4. compare cooperation, fitness, `R_opp`, `R_self`, and the five mean policy parameters across seeds;
5. distinguish common tendencies from seed-specific regime histories;
6. do not infer reproducibility from endpoints alone — inspect trajectories and cross-seed dispersion.

## 12. Resume instruction

Resume at **first 50-generation multi-seed replication and result persistence/analysis**. RNG ownership and reproducibility are validated. Do not revisit basic Prisoner's Dilemma foundations or RNG plumbing unless a regression appears.