# CURRENT_STATE.md

**Last updated:** 2026-09-11  
**Project phase:** Stage 0H — EXP-001 mechanism-level multi-seed analysis  
**Implementation status:** CORE SIMULATOR, FITNESS, SELECTION, MUTATION, RANDOM INITIALIZATION, RUN-OWNED RNG, ALIGNED HISTORY, BEHAVIOURAL OBSERVABLES, POLICY MEAN/STD, CONDITIONAL-RESPONSE OBSERVABLES, FULL MEMORY-ONE DECOMPOSITION AND FIRST PERFORMANCE CLEANUPS IMPLEMENTED  
**Previous gate:** COMPLETED — five-seed trajectories for cooperation, `R_opp`, `R_self`, `mu`, interaction `I`, and `p0` plotted  
**Next task:** diagnose raw policy parameters and state occupancy, especially seed 44 collapse and seed 46 late recovery

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

## 8. Cooperation trajectory comparison

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

## 10. Full policy-decomposition trajectory findings

`mu`, `I`, and `p0` were plotted across seeds together with cooperation and the response indices.

### Baseline level `mu`

`mu` tracks the broad direction of cooperation much more closely than the response contrasts in several runs.

- seed 44: `mu` rises early with the initial cooperation surge, then falls persistently from roughly 0.6 toward about 0.18 while cooperation collapses toward 0.10;
- seed 43: both `mu` and cooperation trend downward strongly;
- seed 42: falling `mu` is consistent with its broad cooperation decline, although local deviations remain;
- seed 46: `mu` recovers only moderately to about 0.4 while observed cooperation rises much more sharply, up to about 0.6.

Thus `mu` explains an important level effect but not the full behaviour, especially seed 46.

### First-round probability `p0`

`p0` is not a plausible main driver of long-run cooperation under `n_rounds=100` because it directly affects only 1 of 100 rounds per match.

The trajectories support this: seed 46's late cooperation recovery occurs while `p0` remains low / declines, and seed 44's cooperation can remain low even while `p0` later rises.

### Interaction `I`

The interaction contrast uses the unscaled convention

`I = p_CC-p_CD-p_DC+p_DD`

so its valid range is `[-2,2]`; values above 1 are not an error.

Seed 46 shows the clearest interaction signal: `I` rises sharply around the same late interval in which cooperation recovers, reaching roughly 1 or above. This suggests the recovered regime develops a strong distinction between homogeneous previous outcomes (`CC`,`DD`) and mixed outcomes (`CD`,`DC`). This is not itself evidence of reciprocity or cooperation because positive `I` can be produced by high `p_CC`, high `p_DD`, or both.

Seed 44, by contrast, loses cooperation mainly while `mu` falls; its interaction remains comparatively modest. This makes seed 44 and seed 46 useful contrasting cases.

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

Separate stored joint `CD_rate` and `DC_rate` can still be useful for diagnostics, but they should not be interpreted directly as unequal population-level self-centric occupancies.

## 13. Runtime / implementation status

Safe performance cleanups implemented:

- Bernoulli action draw via `rng.random() < p`;
- payoff table created once in `Game.__init__`;
- joint state constructed directly as `action_a + action_b`;
- redundant internal `Game.history/_get_stats()` removed;
- run arguments are explicit.

Runtime still scales approximately as:

`G * N(N-1)/2 * n_rounds`.

## 14. Remaining cleanup / methodological work

Non-blocking code cleanup:

- rename function arguments `game_number -> generations` and `players -> population_size` consistently;
- type `Population.policies` as `list[Policy] | None`;
- eventually simplify the `Population` constructor;
- persist configuration/history in a stable serializable format.

Methodological work:

- inspect raw mean-policy trajectories `p_CC,p_CD,p_DC,p_DD`, especially seeds 44 and 46;
- compare them against `CC`, `mixed`, and `DD` occupancy;
- use seed 44 vs seed 46 as a targeted mechanism comparison before adding more seeds;
- record mutation clipping frequency later;
- quantify stochastic fitness noise later.

## 15. Immediate next gate

1. plot the four raw conditional mean-policy parameters for **seed 44 and seed 46**;
2. plot `CC_rate`, `mixed_rate`, and `DD_rate` for those same two runs;
3. diagnose which raw parameter(s) generate seed 46's large positive interaction and late cooperation recovery;
4. diagnose whether seed 44's collapse is primarily a falling baseline level `mu`, a shift in state occupancy, or both;
5. only after this targeted mechanism comparison compute cross-seed aggregate summaries or expand the seed count.

## 16. Resume instruction

Resume at **seed-44 vs seed-46 raw-policy and state-occupancy diagnosis**. `mu`, `R_self`, `R_opp`, `I`, and `p0` trajectories are available for seeds 42-46. Do not infer behaviour from the contrasts alone; combine policy decomposition with occupancy.