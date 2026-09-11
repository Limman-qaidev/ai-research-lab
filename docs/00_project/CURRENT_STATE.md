# CURRENT_STATE.md

**Last updated:** 2026-09-11  
**Project phase:** Stage 0H — EXP-001 reproducible multi-seed preparation  
**Implementation status:** CORE SIMULATOR, FITNESS, SELECTION, MUTATION, RANDOM INITIALIZATION, RUN-OWNED NUMPY GENERATOR, ALIGNED HISTORY, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, CONDITIONAL-RESPONSE OBSERVABLES AND FIRST INNER-LOOP PERFORMANCE CLEANUP IMPLEMENTED; REPRODUCIBILITY GATE PASSED  
**Previous gate:** COMPLETED — same-seed runs reproduce exactly and different seeds diverge from generation 0  
**Next task:** persist run configuration alongside history, then build the first small multi-seed replication runner

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
- NumPy `Generator` is now created inside each experimental run from that run's explicit seed and passed explicitly through initialization, action sampling, selection, and mutation.

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

Per generation the experiment records:

- mean fitness;
- cooperation / defection rate;
- `CC`, `mixed=CD+DC`, `DD` rates;
- mean policy vector;
- policy-parameter standard deviations;
- opponent-response index
  `R_opp = ((p_CC+p_DC) - (p_CD+p_DD))/2`;
- own-action persistence index
  `R_self = ((p_CC+p_CD) - (p_DC+p_DD))/2`.

Both `R_opp` and `R_self` are computed from the mean policy vector. Because these indices are linear in the policy parameters, computing them from the mean policy is equivalent to averaging the same index over individuals.

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

Generation 0:

- mean fitness `2.1965`;
- cooperation `0.4734`;
- `CC=0.2237`, `mixed=0.4994`, `DD=0.2769`;
- mean policy approximately `(0.489,0.533,0.501,0.436,0.467)`;
- `R_opp≈0.0009`;
- `R_self≈0.0659`.

Generation 49:

- mean fitness `1.4174`;
- cooperation `0.1518`;
- `CC=0.0380`, `mixed=0.2275`, `DD=0.7344`;
- mean policy approximately `(0.269,0.568,0.406,0.233,0.090)`;
- standard deviations approximately `(0.099,0.155,0.109,0.130,0.088)`;
- `R_opp≈0.1526`;
- `R_self≈0.3261`.

The run ends in a strongly defection-dominated regime with substantial own-action persistence and weaker positive opponent responsiveness.

## 8. Regime change observed

The 50-generation trajectory is not a smooth move toward AllD or TFT.

### Roughly generations 6–20

`p_DD` becomes very high while `p_DC` becomes very low. Around generation 19:

`(p_CC,p_CD,p_DC,p_DD) ≈ (0.261,0.283,0.114,0.825)`.

This is better described as anti-persistent / alternating structure than reciprocity. By generation 19, `R_opp≈-0.366` and `R_self≈-0.198`.

### Roughly generations 30–49

`p_CC` rises and `p_DD` collapses. At generation 49:

`p0≈0.269`, `p_CC≈0.568`, `p_CD≈0.406`, `p_DC≈0.233`, `p_DD≈0.090`.

Under a mean-policy approximation:

- a fresh symmetric match starts in `DD` with probability `(1-p0)^2 ≈ 0.534`;
- once in `DD`, it stays in `DD` next round with probability `(1-p_DD)^2 ≈ 0.829`.

This explains why high `p_CC` does not rescue aggregate cooperation: many matches enter defection early and become trapped there.

## 9. Reproducibility gate — passed

RNG ownership is now explicit at the run level.

The run creates exactly one generator with:

`rng = np.random.default_rng(seed)`

and the same generator is passed explicitly through:

- initial population sampling;
- stochastic action choice;
- fitness evaluation;
- parent selection;
- Gaussian mutation;
- next-generation construction.

No `Policy` or `Population` object stores hidden RNG state.

A short verification run with `N=30`, `G=5`, `n_rounds=100`, `sigma=0.05` confirmed:

- run A with `seed=42` and run B with `seed=42` were identical generation by generation, including scalar observables and policy arrays;
- run C with `seed=43` differed already at generation 0 in the random population and derived observables.

Therefore the current implementation satisfies the intended computational reproducibility invariant:

`same code + same configuration + same seed -> same trajectory`.

And independent seeds produce distinct stochastic realizations.

## 10. Scientific interpretation so far

For the seed-42 exploratory baseline:

- selection does not maximize mean population fitness;
- cooperation can collapse while conditional policy structure becomes stronger;
- behaviour and policy mechanism evolve on different trajectories;
- own-action history can matter more than opponent history;
- regime transitions can occur within one evolutionary run;
- aggregate cooperation alone is insufficient to classify the evolved mechanism.

These remain exploratory observations from one seed until replicated.

## 11. Runtime / implementation status

Runtime scales approximately as:

`G * N(N-1)/2 * n_rounds`.

Implemented safe performance cleanups:

- action choice uses `rng.random() < p` for Bernoulli action sampling;
- the payoff table is created once in `Game.__init__`;
- joint action state is formed directly with `action_a + action_b`;
- the redundant internal `Game.history/_get_stats()` path has been removed;
- experiment arguments are explicit;
- stochastic dependencies are visible in method signatures.

## 12. Remaining methodological work

Before large replication:

- persist run configuration alongside trajectory (`seed`, `N`, `G`, `n_rounds`, `sigma`);
- preferably make the experiment create its own `Game` from `num_rounds` so the run is fully self-contained;
- optionally clean type annotations such as `str | None` for first-round state and `list[Policy] | None` for population construction;
- add an interaction term for the 2x2 memory-one response table only if scientifically useful;
- record mutation clipping frequency later;
- consider repeated pair matches or another method to quantify stochastic fitness noise.

## 13. Immediate next gate

1. define a run result structure that stores configuration separately from per-generation history;
2. keep one self-contained experiment interface such as `(seed, N, G, n_rounds, sigma) -> run_result`;
3. verify the stored configuration is sufficient to reproduce a run;
4. then execute the first small multi-seed batch with the same baseline configuration;
5. compare cooperation, mean fitness, `R_opp`, `R_self`, and the five policy parameters across seeds;
6. only then assess whether defection trapping, regime transitions, opponent responsiveness, and own-action persistence are reproducible.

## 14. Resume instruction

Resume at **run-result design and first multi-seed replication**. Run-level RNG ownership and computational reproducibility are validated. Do not revisit RNG plumbing, Prisoner's Dilemma foundations, or already validated evolutionary operators unless a regression appears.