# CURRENT_STATE.md

**Last updated:** 2026-09-11  
**Project phase:** Stage 0H — EXP-001 finite-population / mechanism analysis  
**Implementation status:** CORE SIMULATOR, FITNESS, SELECTION, MUTATION, RANDOM INITIALIZATION, RUN-OWNED RNG, ALIGNED HISTORY, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, CONDITIONAL-RESPONSE OBSERVABLES, FULL MEMORY-ONE DECOMPOSITION AND FIRST PERFORMANCE CLEANUPS IMPLEMENTED  
**Previous gate:** COMPLETED — exploratory `N=100, G=100, n_rounds=100` five-seed batch compared qualitatively with the earlier `N=30` batch  
**Next task:** test the population-size hypothesis with a controlled same-seed comparison and quantify between-seed dispersion

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
- `R_self = ((p_CC+p_CD) - (p_DC+p_DD))/2`;
- `mu = (p_CC+p_CD+p_DC+p_DD)/4`;
- interaction contrast `I = p_CC-p_CD-p_DC+p_DD`;
- `p0`.

`R_opp` measures average response to the opponent's previous action. `R_self` measures persistence of the agent's own previous action. `mu` measures the unweighted baseline level of conditional cooperation. `I` measures the 2x2 interaction / same-vs-mixed conditional structure. `p0` controls only the first round.

Together `(mu, R_self, R_opp, I)` are a complete linear reparameterization of `(p_CC,p_CD,p_DC,p_DD)`.

## 6. Run-owned RNG and reproducibility — validated

`experiment(seed, ...)` creates a fresh `np.random.default_rng(seed)` and passes the same run-owned generator through initial population generation, stochastic policy actions, parent selection, and Gaussian mutation.

`Policy` and `Population` do not hide RNG state as attributes.

Reproducibility test:

- seed `42` repeated twice produced identical generation-by-generation histories;
- seed `43` differed already at generation 0.

Invariant validated:

`same code + same configuration + same seed -> same trajectory`.

## 7. First 50-generation replication batch

Configuration held fixed:

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

The endpoints show substantial cross-seed variation. `R_self > R_opp` is not universal, so the seed-42 late-state interpretation cannot be generalized.

## 8. Cooperation trajectory comparison at N=30

The five cooperation trajectories are strongly non-monotonic and diverge despite similar initial values near 0.5.

- seed 44 rises above 0.6 very early and then progressively collapses to about 0.10;
- seed 43 drops early and remains mostly in a low-cooperation regime, ending near 0.09;
- seed 42 declines more gradually, partially recovers in the late 30s, then collapses to about 0.15;
- seed 45 shows several reversals and ends around 0.25;
- seed 46 declines toward roughly 0.3-0.35, then undergoes a strong late recovery around generations 34-41, peaks near 0.6, and ends around 0.45.

Endpoint averages therefore hide important path dependence and apparent regime transitions. These runs suggest, but do not prove, multiple metastable regions.

## 9. Conditional-response comparison

`R_opp` and `R_self` are contrasts, not cooperation levels.

Important observations:

- seed 42 moves from strongly negative response indices in its middle phase to positive values later while cooperation still collapses;
- seed 44 reaches cooperation above 0.6 with only modest response indices, then loses cooperation while conditional structure remains positive;
- seed 45 develops the strongest final opponent-response signal (`R_opp≈0.47`) but ends with only moderate cooperation;
- seed 46's late recovery coincides with `R_self` becoming positive, while `R_opp` was already positive.

Therefore positive `R_opp` or `R_self` is not sufficient for high aggregate cooperation.

## 10. Full policy-decomposition findings at N=30

`mu`, `I`, and `p0` were plotted across seeds together with cooperation and the response indices.

- `mu` tracks the broad direction of cooperation more closely than the response contrasts in several runs;
- seed 44's cooperation collapse accompanies a large decline in `mu`;
- seed 46's late recovery is larger than its `mu` recovery and coincides with a large positive interaction `I`, suggesting occupancy / conditional-structure effects;
- `p0` does not track long-run cooperation and is expected to be weakly selected at `n_rounds=100` because it directly affects only the first round.

The interaction uses the unscaled convention `I = p_CC-p_CD-p_DC+p_DD`, valid range `[-2,2]`.

## 11. Key mathematical interpretation

The raw conditional probabilities can be reconstructed from the decomposition:

- `p_CC = mu + R_self/2 + R_opp/2 + I/4`;
- `p_CD = mu + R_self/2 - R_opp/2 - I/4`;
- `p_DC = mu - R_self/2 + R_opp/2 - I/4`;
- `p_DD = mu - R_self/2 - R_opp/2 + I/4`.

Observed cooperation is not determined by these coefficients alone. It also depends on how often the process visits each previous state.

Conceptually:

`observed cooperation ≈ policy probabilities weighted by state occupancy`.

For a long match, a mean-policy diagnostic can be written approximately as

`C ≈ q_CC*p_CC + q_CD*p_CD + q_DC*p_DC + q_DD*p_DD`,

with a small first-round correction from `p0`. Exact population behaviour also depends on heterogeneity and correlations between individual policies and the states they occupy.

## 12. Important occupancy bookkeeping note

The simulator records joint ordered outcomes `CC`, `CD`, `DC`, `DD` from the `(policy_a, policy_b)` match orientation. For population-level self-centric state occupancy, a joint mixed outcome contributes one `CD` state to one player and one `DC` state to the other regardless of whether the stored joint outcome is `CD` or `DC`.

Therefore aggregate player-perspective mixed-state occupancy satisfies

`q_CD = q_DC = mixed_rate/2`.

## 13. Exploratory population-size pilot: N=100

Jonathan increased the population from `N=30` to `N=100` and extended the run to `G=100`, keeping `n_rounds=100` and the same five seeds `42..46`.

Qualitative observations from the new trajectories:

- cooperation becomes much more similar across seeds than in the `N=30` batch;
- all five runs decline from initial cooperation near 0.5 toward a low-cooperation band, roughly around 0.1-0.2 after the early/middle generations;
- the large seed-specific late recovery seen for seed 46 at `N=30` is absent in the `N=100` trajectories;
- `mu` also converges much more tightly, around a low conditional-cooperation level;
- `p0` remains highly divergent across seeds even when observed cooperation is similar, consistent with `p0` being weakly selected over 100-round matches;
- `R_opp`, `R_self`, and `I` retain substantial cross-seed structural variation despite more similar aggregate cooperation.

This suggests a possible **behavioural convergence with policy-parameter degeneracy**: larger populations may produce more reproducible low-cooperation behaviour while allowing different conditional policy structures to realize it.

### Why population size can matter in this implementation

Increasing `N` changes several finite-population mechanisms simultaneously:

1. the random initial population mean is sampled more precisely (`SE(mean) ~ 1/sqrt(N)`);
2. fitness-proportional reproduction has weaker multinomial sampling noise in frequencies (`~1/sqrt(N)`);
3. each individual is evaluated against `N-1` opponents, so larger `N` also reduces stochastic/opponent-composition noise in fitness;
4. more offspring are produced each generation, increasing mutational supply and sampling the mutation distribution more densely.

Therefore the current implementation structurally couples **population size** and **fitness-evaluation sample size**.

### Important control point

Changing total run length from `G=50` to `G=100` does not affect generations `0..49` because the simulator has no look-ahead. Therefore the first 50 generations of the `N=100` runs can be compared directly against the previous `N=30, G=50` runs, provided `n_rounds`, `sigma`, seeds, and code path are otherwise unchanged.

The endpoint at generation 99, however, cannot be compared to generation 49 as a pure population-size effect.

## 14. Runtime / implementation status

Safe performance cleanups implemented:

- Bernoulli action draw via `rng.random() < p`;
- payoff table created once in `Game.__init__`;
- joint state constructed directly as `action_a + action_b`;
- redundant internal `Game.history/_get_stats()` removed;
- run arguments are explicit.

Runtime scales approximately as:

`G * N(N-1)/2 * n_rounds`.

`N=100, G=100, n_rounds=100` implies about `49.5 million` game rounds per seed, so controlled replication should account for quadratic scaling in `N`.

## 15. Remaining cleanup / methodological work

Non-blocking code cleanup:

- rename function arguments `game_number -> generations` and `players -> population_size` consistently;
- type `Population.policies` as `list[Policy] | None`;
- eventually simplify the `Population` constructor;
- persist configuration/history in a stable serializable format.

Methodological work:

- quantify the population-size effect rather than relying on visual comparison;
- compare cross-seed means and dispersion of cooperation and `mu` for `N=30` vs `N=100` over the common first 50 generations;
- compare policy diversity (`std_policy`) and between-seed parameter dispersion;
- decide whether to decouple population size from number of opponents used for fitness evaluation in a later experiment;
- inspect raw policy / occupancy mechanisms after the finite-population comparison;
- record mutation clipping frequency later;
- quantify stochastic fitness noise later.

## 16. Immediate next gate

1. treat the existing `N=30` and `N=100` runs as an exploratory population-size pilot;
2. over generations `0..49`, compute for each `N` and generation the cross-seed mean and standard deviation (or quantiles) of `cooperation_rate` and `mu`;
3. compare how between-seed dispersion evolves with `N`;
4. compare `std_policy` to test whether larger `N` changes within-population diversity as well as between-run variability;
5. if the effect persists, run a controlled confirmatory grid with the same seeds and fixed `G`, `n_rounds`, and `sigma` across population sizes;
6. distinguish two hypotheses: larger `N` changes the mean evolutionary trajectory vs larger `N` mainly suppresses finite-population stochasticity.

## 17. Resume instruction

Resume at **controlled population-size analysis**. The `N=100` pilot visually shows much lower cross-seed variation in cooperation than `N=30`, but this is not yet a causal conclusion. Quantify cross-seed dispersion over the common first 50 generations and keep population size separate conceptually from the number of opponent evaluations used to estimate fitness.