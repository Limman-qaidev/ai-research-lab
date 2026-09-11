# CURRENT_STATE.md

**Last updated:** 2026-09-11  
**Project phase:** Stage 0H — EXP-001 multi-seed trajectory analysis  
**Implementation status:** CORE SIMULATOR, FITNESS, SELECTION, MUTATION, RANDOM INITIALIZATION, RUN-OWNED RNG, ALIGNED HISTORY, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, CONDITIONAL-RESPONSE OBSERVABLES AND FIRST PERFORMANCE CLEANUPS IMPLEMENTED  
**Previous gate:** COMPLETED — first five-seed cooperation and conditional-response trajectory comparison  
**Next task:** decompose the mean memory-one policy into baseline level, self/opponent effects and interaction, then relate those components to behaviour and state occupancy

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

The late population is not TFT-like. Own-action persistence is stronger than opponent responsiveness for this seed.

## 7. Run-owned RNG — validated

The module-global RNG has been removed.

`experiment(seed, ...)` creates a fresh `np.random.default_rng(seed)` and explicitly passes that same run-owned generator through:

- initial population generation;
- stochastic policy actions;
- parent selection;
- Gaussian mutation.

`Policy` and `Population` do not hide RNG state as object attributes.

Reproducibility test:

- seed `42` repeated twice produced identical generation-by-generation histories;
- seed `43` differed already at generation 0.

Invariant validated:

`same code + same configuration + same seed -> same trajectory`.

## 8. Multi-seed runner — implemented

`experiment()` returns run configuration separately from generation history.

Current run configuration contains:

- seed;
- generation count (`game_number`, pending rename to `generations`);
- population size (`players`, pending rename to `population_size`);
- number of rounds;
- mutation sigma.

The runner stores multiple runs as a collection of `configuration + history` records.

## 9. First 50-generation replication batch

Configuration held fixed across runs:

- `N=30`;
- `G=50`;
- `n_rounds=100`;
- `sigma=0.05`;
- seeds `42,43,44,45,46`.

Generation-49 endpoints:

| seed | cooperation | R_opp | R_self |
| ---: | ---: | ---: | ---: |
| 42 | 0.1518 | 0.1526 | 0.3261 |
| 43 | 0.0931 | 0.1316 | 0.0345 |
| 44 | 0.1006 | 0.1036 | 0.1519 |
| 45 | 0.2515 | 0.4715 | 0.2619 |
| 46 | 0.4509 | 0.1659 | 0.1783 |

The endpoints show substantial cross-seed variation. `R_self > R_opp` is not universal, so the seed-42 late-state interpretation cannot be generalized to all runs.

All five final `R_opp` and `R_self` values are positive in this first batch, but five seeds are insufficient to establish that as a reproducible population-level result.

## 10. Cooperation trajectory comparison

A `seed x generation` cooperation matrix was constructed and visualized for all five runs.

The trajectories are strongly non-monotonic and diverge qualitatively despite similar random-initialization cooperation levels near 0.5:

- seed 44 rises above 0.6 very early and then progressively collapses to about 0.10;
- seed 43 drops comparatively early and remains mostly in a low-cooperation regime, ending near 0.09;
- seed 42 declines more gradually, shows a partial recovery around the late 30s, then collapses to about 0.15;
- seed 45 shows several reversals and ends around 0.25;
- seed 46 first declines into a roughly 0.3-0.35 regime, then undergoes a strong late recovery around generations 34-41, peaking near 0.6 and ending around 0.45.

Endpoint averages therefore hide important path dependence and apparent regime transitions. These trajectories suggest, but do not prove, multiple metastable regions in the evolutionary dynamics.

## 11. Conditional-response trajectory comparison

`R_opp` and `R_self` were plotted across all five seeds and compared with cooperation.

Important observations:

- seed 42 moves from strongly negative `R_opp` / negative `R_self` in its middle phase to positive values later, while cooperation still collapses; therefore positive conditional-response indices are not sufficient for high cooperation;
- seed 44 reaches cooperation above 0.6 while `R_opp` and `R_self` are only modest, then later loses cooperation while conditional structure remains positive;
- seed 45 develops the strongest final opponent-response signal (`R_opp≈0.47`) but ends with only moderate cooperation (`≈0.25`);
- seed 46's late cooperation recovery coincides with `R_self` moving from near-zero/negative values to clearly positive values, while `R_opp` is already positive and changes more modestly; this is suggestive but not causal evidence.

The comparison establishes that `R_opp` and `R_self` measure **contrasts** in the conditional policy, not its overall cooperation level. They cannot by themselves explain observed cooperation.

For the four conditional probabilities define the grand mean

`mu = (p_CC + p_CD + p_DC + p_DD)/4`

and interaction contrast

`I = p_CC - p_CD - p_DC + p_DD`.

Together, `(mu, R_self, R_opp, I)` form a complete linear reparameterization of `(p_CC,p_CD,p_DC,p_DD)`. `p0` remains a separate first-round parameter.

Observed cooperation additionally depends on **state occupancy**: conditional probabilities are weighted by how often `CC,CD,DC,DD` are actually visited. Thus similar response contrasts can produce very different aggregate behaviour.

## 12. Runtime / implementation status

Safe performance cleanups implemented:

- Bernoulli action draw via `rng.random() < p`;
- payoff table created once in `Game.__init__`;
- joint state constructed directly as `action_a + action_b`;
- redundant internal `Game.history/_get_stats()` removed;
- run arguments are explicit.

Runtime still scales approximately as:

`G * N(N-1)/2 * n_rounds`.

## 13. Remaining cleanup / methodological work

Non-blocking code-contract cleanup:

- rename `game_number` -> `generations`;
- rename `players` -> `population_size`;
- type `Population.policies` as `list[Policy] | None` because `None` triggers random initialization;
- eventually simplify the `Population` constructor because `N` and `rng` are irrelevant when an explicit policy list is provided.

Methodological work:

- persist configuration and history in a stable serializable format; NumPy arrays/scalars need conversion before JSON serialization;
- compute and plot `p0`, `mu`, and interaction `I` across seeds;
- inspect raw mean-policy parameter trajectories where needed, especially seeds 44 and 46;
- compare policy decomposition against `CC/CD/DC/DD` state occupancy;
- record mutation clipping frequency later;
- quantify stochastic fitness noise later via repeated matches, longer horizons, or uncertainty analysis.

## 14. Immediate next gate

1. derive and implement `mu` and interaction contrast `I` from the mean policy;
2. plot `p0`, `mu`, and `I` across the five seeds;
3. compare those trajectories with cooperation and `DD_rate`, especially seed 44's collapse and seed 46's late recovery;
4. use the existing state-rate observables to test whether behaviour changes are driven by policy level, conditional structure, state occupancy, or combinations of them;
5. only after mechanism-level diagnosis compute cross-seed summaries such as means, dispersion, or quantiles.

## 15. Resume instruction

Resume at **complete memory-one policy decomposition and state-occupancy diagnosis**. Cooperation, `R_opp`, and `R_self` trajectories for seeds 42-46 are available. Do not infer cooperation directly from response indices: they are contrasts, not levels, and behaviour also depends on state occupancy.
