# EXP-001 — Evolutionary Iterated Prisoner's Dilemma

**Status:** EXPLORATORY RUNNING  
**Created:** 2026-09-07  
**Last updated:** 2026-09-11

## 1. Research question

Can reciprocal cooperation emerge and persist under population-based evolutionary selection when agents are restricted to stochastic memory-one policies in an Iterated Prisoner's Dilemma, and how do interaction horizon and evolutionary operators affect the resulting population dynamics?

The first implemented version asks a narrower operational question: starting from a genuinely random population of stochastic memory-one policies, what behavioural and policy-space structure appears under fitness-proportional selection and local Gaussian mutation?

## 2. Motivation

EXP-001 is the first application laboratory for the project's bottom-up study of adaptive systems. It is intentionally small enough to understand completely while containing mechanisms that recur later in reinforcement learning, self-play, multi-agent learning, population methods, and automated discovery:

- state / observation;
- policy representation;
- repeated interaction;
- reward / fitness;
- mutation / exploration;
- selection;
- inheritance;
- frequency-dependent performance;
- emergence at population level.

The experiment is not intended to discover a commercially useful strategy. Its purpose is to understand how local behavioural rules and evolutionary pressure generate population-level dynamics.

## 3. Hypotheses

- `H0:` evolutionary selection over stochastic memory-one policies does not produce a reproducible increase or persistence of reciprocal cooperation beyond what is explained by initialization, drift, and the chosen operators.
- `H1:` under sufficiently repeated interaction, evolutionary selection can produce and/or maintain policy regions that support reciprocal cooperation and resist unrestricted exploitation.

Secondary hypotheses:

- longer effective interaction horizons should favour reciprocal strategies relative to one-shot exploitation;
- excessive selection pressure or insufficient mutation may reduce diversity and create premature population concentration;
- mutation scale should create an exploration-versus-local-refinement trade-off.

No hypothesis is considered confirmed by the exploratory runs below.

## 4. System under study

### Environment

Two-player repeated Prisoner's Dilemma.

Stage actions:

`A = {C, D}`

Payoffs:

- `T = 5` — self defects while opponent cooperates;
- `R = 3` — mutual cooperation;
- `P = 1` — mutual defection;
- `S = 0` — self cooperates while opponent defects.

Thus:

`T > R > P > S`

and:

`2R > T + S`.

The implemented v1 environment uses a fixed finite horizon of `n_rounds = 100` per match.

### Policy representation

Each candidate is a stochastic memory-one policy:

`pi = (p0, p_CC, p_CD, p_DC, p_DD) in [0,1]^5`

with self action written first.

- `p0 = P(C_1)`;
- `p_xy = P(C_t | previous state = xy)` for later rounds.

`p0` evolves together with the four memory-one parameters.

Reference deterministic policies:

- `AllC = (1,1,1,1,1)`;
- `AllD = (0,0,0,0,0)`;
- `TFT = (1,1,0,1,0)`.

## 5. Fitness, selection, mutation and inheritance

### Population evaluation

Every unordered pair of individuals plays one `n_rounds` match. No self-play is used for fitness.

For population size `N`:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

Fitness is therefore frequency-dependent on the current population.

### Selection

Fitness-proportional sampling with replacement:

`q_i = F_i / sum_j(F_j)`.

Exactly `N` parents are sampled.

### Mutation

Every selected parent produces one child with additive Gaussian mutation:

`epsilon_k ~ Normal(0, sigma^2)`

`pi_child = clip(pi_parent + epsilon, 0, 1)`.

Current exploratory value:

`sigma = 0.05`.

The child is a new policy object, preserving the previous generation.

## 6. Validated baselines

For `n_rounds = 100`, the simulator reproduces the deterministic reference results exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

Population-level analytical checks also matched the implementation for `[TFT, TFT, AllD]`.

## 7. Observables

Each generation records exactly one stochastic population evaluation. The same fitness vector is used both for the recorded generation and for parent selection.

Behavioural observables:

- mean population fitness;
- cooperation rate;
- defection rate;
- `CC_rate`;
- `mixed_rate = CD + DC`;
- `DD_rate`.

Policy-space observables:

- mean policy vector;
- standard deviation of each policy parameter.

Two useful conditional-response summaries are now distinguished.

### Opponent-action effect

Because the opponent previously cooperated in `CC` and `DC`, and defected in `CD` and `DD`:

`R_opp = ((p_CC + p_DC) - (p_CD + p_DD)) / 2`.

Positive values mean greater average willingness to cooperate after opponent cooperation than after opponent defection.

### Own-action persistence effect

Because self previously cooperated in `CC` and `CD`, and defected in `DC` and `DD`:

`R_self = ((p_CC + p_CD) - (p_DC + p_DD)) / 2`.

Positive values mean greater willingness to cooperate after having cooperated previously than after having defected previously. This is path dependence / behavioural inertia, not reciprocity.

A single `R_opp` statistic must therefore not be treated as a complete reciprocity measure.

## 8. Reproducibility

Current stochastic mechanisms use one NumPy generator:

`rng = np.random.default_rng(42)`.

It drives:

- random initial population generation;
- action sampling;
- parent selection;
- mutation.

The generator is still module-global. This is acceptable for exploratory single-run work but must become run-owned before formal independent multi-seed experiments.

## 9. Exploratory run A — implementation/science check

Configuration:

- `N = 100`;
- `n_rounds = 100`;
- `G = 10`;
- `sigma = 0.05`;
- seed `42`.

Observed generation 0 -> 9:

- mean fitness: `2.2228 -> 1.8075`;
- cooperation: `0.4868 -> 0.3040`;
- `DD_rate`: `0.2640 -> 0.4965`.

This short run showed declining aggregate cooperation and increasing mutual defection. It was not long enough to interpret a stable regime.

## 10. Exploratory run B — 50 generations

Configuration:

- `N = 30`;
- `n_rounds = 100`;
- `G = 50`;
- `sigma = 0.05`;
- seed `42`.

This configuration contains `435` pairwise matches and `43,500` played rounds per generation, or `2,175,000` played rounds across 50 generations.

### End-to-end behaviour

Generation 0:

- mean fitness `2.1965`;
- cooperation `0.4734`;
- `CC = 0.2237`;
- `mixed = 0.4994`;
- `DD = 0.2769`;
- mean policy approximately `(0.489, 0.533, 0.501, 0.436, 0.467)`.

Generation 49:

- mean fitness `1.4174`;
- cooperation `0.1518`;
- `CC = 0.0380`;
- `mixed = 0.2275`;
- `DD = 0.7344`;
- mean policy approximately `(0.269, 0.568, 0.406, 0.233, 0.090)`;
- policy standard deviations approximately `(0.099, 0.155, 0.109, 0.130, 0.088)`.

Aggregate cooperation therefore collapsed substantially in this seed while mutual defection became dominant.

### Regime structure

The trajectory is not monotonic in policy space.

#### Early / middle regime: roughly generations 6–20

A striking configuration appears in which `p_DD` becomes very high while `p_DC` becomes very low. Around generation 19:

- `p_CC ≈ 0.261`;
- `p_CD ≈ 0.283`;
- `p_DC ≈ 0.114`;
- `p_DD ≈ 0.825`.

For a representative identical policy, `DD` would then tend to move toward cooperation, while `CC` would tend to break down. This resembles alternating or anti-persistent mutual-state dynamics rather than TFT-like reciprocity.

#### Later regime: roughly generations 30–49

`p_CC` rises while `p_DD` falls strongly. By generation 49:

- `p_CC ≈ 0.568`;
- `p_CD ≈ 0.406`;
- `p_DC ≈ 0.233`;
- `p_DD ≈ 0.090`;
- `p0 ≈ 0.269`.

This makes mutual defection very sticky. Under the mean-policy approximation:

- probability that a fresh symmetric encounter starts in `DD` is approximately `(1-p0)^2 ≈ 0.534`;
- once in `DD`, probability of remaining in `DD` next round is approximately `(1-p_DD)^2 ≈ 0.829`.

This provides a direct mechanism for the observed low cooperation and high `DD_rate` late in the run.

The mean-policy Markov approximation at generation 49 predicts long-run cooperation around `0.167`, close to the observed population cooperation `0.152`. This is only a diagnostic approximation because the real population remains heterogeneous.

## 11. Conditional-response decomposition

The 50-generation run shows why one scalar 'reciprocity' index is insufficient.

At generation 0:

- `R_opp ≈ +0.001`;
- `R_self ≈ +0.066`.

At generation 49:

- `R_opp ≈ +0.153`;
- `R_self ≈ +0.326`.

Thus there is a positive late-run opponent-action effect, but the stronger signal is own-action persistence. The population is substantially more likely to cooperate after it previously cooperated than after it previously defected.

This is not TFT. TFT would require approximately:

`(p0, p_CC, p_CD, p_DC, p_DD) = (1,1,0,1,0)`.

The generation-49 mean has low `p_DC` and moderate `p_CD`, the opposite of a key TFT asymmetry. The late-run population should therefore be described as path-dependent and defection-trapping, not as having evolved TFT-like reciprocal cooperation.

## 12. Interpretation

For this seed and operator configuration:

1. selection does not maximize mean population fitness;
2. cooperation can decline even while structured conditional behaviour emerges;
3. the evolved structure is not captured by cooperation rate alone;
4. policy dependence on self history can be stronger than policy dependence on opponent history;
5. high `p_CC` is not sufficient for cooperative population behaviour when `p0` is low and `p_DD` makes defection states sticky;
6. the trajectory appears to pass through qualitatively different policy regimes rather than moving smoothly toward one canonical strategy.

These are exploratory observations, not general conclusions.

## 13. Performance note

The simulation cost is dominated by Python-level repeated-game execution. For population size `N`, horizon `n`, and `G` generations, played rounds scale as:

`G * N(N-1)/2 * n`.

Safe implementation optimizations that do not change the model include:

- avoid rebuilding the payoff dictionary every played round;
- avoid temporary NumPy arrays for two actions when constructing a state;
- consider Bernoulli sampling via one uniform random draw instead of `rng.choice` for binary actions.

Any optimization that changes the random-number consumption order will create a different seeded trajectory even if it preserves the same probability law, so baseline runs should be retained for comparison.

## 14. Limitations

- only one seed has been examined in the 50-generation run;
- `N = 30` increases stochastic drift relative to larger populations;
- each stochastic policy pair is evaluated through only one 100-round match per generation;
- mutation uses clipping, which can bias probability mass near boundaries;
- the population is summarized by means/stds, which can hide multimodality;
- mean-policy Markov calculations are approximations, not the actual heterogeneous-population dynamics;
- fixed finite horizon remains the only termination model tested;
- memory-one policies exclude longer-history strategies and explicit opponent models.

## 15. Immediate next questions

Before claiming emergence or failure of reciprocity:

1. Should conditional-response observability include `R_opp`, `R_self`, and an interaction term rather than a single reciprocity index?
2. Does the late defection-trapping regime recur across independent seeds?
3. Is the apparent regime transition robust to population size?
4. How much of the trajectory is stochastic drift versus systematic selection?
5. Does reducing fitness-estimation noise by repeated pair matches materially change the dynamics?
6. How sensitive is the result to mutation scale and clipping?

## 16. Next experimental gate

1. remove redundant internal `Game.history` / `_get_stats()` if still present;
2. add explicit `R_opp` and `R_self` observables to each history row;
3. retain the complete 50-generation seed-42 run as a baseline;
4. make run-level RNG ownership explicit;
5. optimize only obvious inner-loop overhead while preserving the probabilistic model;
6. then repeat the same experimental configuration across multiple seeds before making a scientific claim.

## 17. Learning reflection

The first complete runs demonstrate why aggregate reward or cooperation alone is insufficient for understanding evolutionary multi-agent systems. The same population can display changing fitness, changing behavioural outcomes, and changing conditional policy structure on different timescales. The main methodological lesson is to distinguish observable behaviour from the mechanism encoded in the evolving policy, and to separate response to the opponent from persistence of the agent's own prior behaviour.