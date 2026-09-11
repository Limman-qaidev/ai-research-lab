# CURRENT_STATE.md

**Last updated:** 2026-09-11  
**Project phase:** Stage 0G — EXP-001 exploratory evolutionary dynamics  
**Implementation status:** CORE SIMULATOR, FITNESS, SELECTION, MUTATION, RANDOM INITIALIZATION, EXPLICIT NUMPY GENERATOR, ALIGNED HISTORY, BEHAVIOURAL OBSERVABLES AND POLICY MEAN/STD IMPLEMENTED; 10-GENERATION AND 50-GENERATION EXPLORATORY RUNS COMPLETED  
**Previous gate:** COMPLETED — first longer exploratory trajectory analysed mechanistically  
**Next task:** separate opponent-response from own-action persistence in observables, clean redundant history state, then prepare efficient multi-seed runs

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise mechanisms, derive and reproduce them, critique experiments, and eventually design original techniques, systems, or hypotheses.

The pedagogical sequence remains:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`.

## 2. Active experiment

**EXP-001 — Evolutionary Iterated Prisoner's Dilemma**  
Detailed experiment record: `docs/03_experiments/EXP-001_EVOLUTIONARY_IPD.md`.

Research question: can reciprocal cooperation emerge and persist under population-based evolutionary selection over stochastic memory-one policies, and what dynamics do the chosen horizon and evolutionary operators generate?

Implemented policy:

`pi = (p0, p_CC, p_CD, p_DC, p_DD)`.

Self action is written first in `CC, CD, DC, DD`.

## 3. Implemented v1 design

- payoffs: `T=5, R=3, P=1, S=0`;
- fixed match horizon: `n_rounds=100`;
- full round-robin population evaluation, one match per unordered pair;
- fitness: `F_i = total_payoff_i / ((N-1)*n_rounds)`;
- fitness-proportional parent selection with replacement;
- Gaussian mutation on all five policy components;
- clipping to `[0,1]`;
- exploratory mutation scale `sigma=0.05`;
- random initial populations sampled independently from `Uniform(0,1)^5`;
- NumPy `Generator` seeded with `42` currently drives all stochastic mechanisms.

Exactly one stochastic evaluation is performed per generation. The same fitness vector is recorded and reused for selection.

## 4. Validated baselines

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
- policy-parameter standard deviations.

The next observability refinement must distinguish:

`R_opp = ((p_CC+p_DC) - (p_CD+p_DD))/2`

from:

`R_self = ((p_CC+p_CD) - (p_DC+p_DD))/2`.

`R_opp` measures average response to the opponent's previous action. `R_self` measures persistence of the agent's own previous action. A positive `R_opp` alone must not be labelled full reciprocity.

## 6. Exploratory run A

Configuration: `N=100`, `G=10`, `n_rounds=100`, `sigma=0.05`, seed `42`.

Generation 0 -> 9:

- mean fitness `2.2228 -> 1.8075`;
- cooperation `0.4868 -> 0.3040`;
- `DD_rate 0.2640 -> 0.4965`.

This established that aggregate cooperation could fall rapidly under the implemented evolutionary process.

## 7. Exploratory run B — longer baseline

Configuration: `N=30`, `G=50`, `n_rounds=100`, `sigma=0.05`, seed `42`.

This run executes `435` pairwise matches and `43,500` game rounds per generation, `2,175,000` game rounds total.

Generation 0:

- mean fitness `2.1965`;
- cooperation `0.4734`;
- `CC=0.2237`, `mixed=0.4994`, `DD=0.2769`;
- mean policy approximately `(0.489,0.533,0.501,0.436,0.467)`.

Generation 49:

- mean fitness `1.4174`;
- cooperation `0.1518`;
- `CC=0.0380`, `mixed=0.2275`, `DD=0.7344`;
- mean policy approximately `(0.269,0.568,0.406,0.233,0.090)`;
- standard deviations approximately `(0.099,0.155,0.109,0.130,0.088)`.

The run therefore ends in a strongly defection-dominated regime.

## 8. Regime change observed

The 50-generation trajectory is not a smooth move toward AllD or TFT.

### Roughly generations 6–20

`p_DD` becomes very high while `p_DC` becomes very low. Around generation 19:

`(p_CC,p_CD,p_DC,p_DD) ≈ (0.261,0.283,0.114,0.825)`.

This implies an unusual mutual-state dynamic: after `DD`, cooperation becomes likely; after `CC`, defection becomes comparatively likely. It is better described as anti-persistent / alternating structure than reciprocity.

### Roughly generations 30–49

`p_CC` rises and `p_DD` collapses. At generation 49:

`p0≈0.269`, `p_CC≈0.568`, `p_CD≈0.406`, `p_DC≈0.233`, `p_DD≈0.090`.

Under a mean-policy approximation:

- a fresh symmetric match starts in `DD` with probability `(1-p0)^2 ≈ 0.534`;
- once in `DD`, it stays in `DD` next round with probability `(1-p_DD)^2 ≈ 0.829`.

This explains why high `p_CC` does not rescue aggregate cooperation: many matches enter defection early and become trapped there.

The stationary self-play approximation of the generation-49 mean policy predicts cooperation around `0.167`, close to the observed `0.152`; this is diagnostic only because the true population remains heterogeneous.

## 9. Key conceptual correction

The late population is **not TFT-like**.

At generation 49:

- opponent-action effect `R_opp ≈ +0.153`;
- own-action persistence effect `R_self ≈ +0.326`.

The stronger signal is therefore persistence / path dependence on the agent's own previous action, not pure opponent reciprocity.

TFT would require approximately `(1,1,0,1,0)`. The late mean instead has low `p_DC` and moderate `p_CD`, which violates a central TFT asymmetry.

Do not describe EXP-001 as having discovered TFT or established reciprocal cooperation.

## 10. Scientific interpretation so far

For this seed and configuration:

- selection does not maximize mean population fitness;
- cooperation can collapse while conditional policy structure becomes stronger;
- behaviour and policy mechanism evolve on different trajectories;
- own-action history can matter more than opponent history;
- regime transitions can occur within one evolutionary run;
- aggregate cooperation alone is insufficient to classify the evolved mechanism.

All of these remain exploratory observations from one seed.

## 11. Runtime / implementation note

The simulator is slow because game execution remains Python-loop heavy. Runtime scales as:

`G * N(N-1)/2 * n_rounds`.

Safe optimizations before multi-seed work:

- move the payoff table out of the per-round `payoff()` reconstruction path;
- avoid creating a temporary NumPy action array merely to form `CC/CD/DC/DD`;
- consider `rng.random() < p` for binary Bernoulli actions instead of `rng.choice`.

These preserve the probabilistic model but can change the exact random-number stream, so the current baseline results must be retained.

## 12. Remaining cleanup

- remove redundant internal `Game.history` / `_get_stats()` if still present;
- make experiment arguments explicit rather than relying on unsafe `None` defaults;
- add `R_opp` and `R_self` to history rows;
- eventually add an interaction term for the 2x2 memory-one response table;
- make RNG ownership explicit per run;
- record mutation clipping frequency later;
- consider repeated pair matches or another method to quantify stochastic fitness noise.

## 13. Immediate next gate

1. clean redundant history state;
2. instrument `R_opp` and `R_self` explicitly;
3. make the obvious inner-loop performance improvements;
4. preserve the 50-generation seed-42 trajectory as the baseline;
5. then repeat the same configuration across multiple independent seeds;
6. only after that assess whether defection trapping, regime transitions, or opponent-responsive behaviour are reproducible.

## 14. Resume instruction

Resume at **conditional-response decomposition, performance cleanup, and preparation for multi-seed replication**. Do not revisit basic Prisoner's Dilemma foundations or already validated evolutionary operators unless a regression appears.