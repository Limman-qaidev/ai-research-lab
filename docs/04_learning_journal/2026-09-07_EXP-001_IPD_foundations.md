# 2026-09-07 — EXP-001 / Iterated Prisoner's Dilemma foundations

## What I was trying to understand

Build enough game-theoretic understanding to justify the representation and evolutionary mechanics of EXP-001 before writing code.

## What I could explain before the session

Jonathan already had the broad intuition that repeated interaction can make cooperation relevant and that evolutionary selection should favour higher-scoring policies, but the precise distinctions among stage-game incentives, policy representation, environment, fitness, and evolutionary dynamics were not yet stable.

## What changed in my understanding

- In the one-shot Prisoner's Dilemma, defection strictly dominates cooperation because `T > R` and `P > S`.
- `R > P` creates the social tension: mutual cooperation is better for both than mutual defection despite individual incentives to defect.
- `2R > T + S` makes sustained mutual cooperation preferable, on average, to alternating exploitation; it does not guarantee that cooperation emerges.
- Repetition changes the strategic problem because current actions can affect future opponent behaviour and therefore future payoff.
- A stochastic memory-one policy conditions only on the immediately preceding joint action.
- With self action written first, the conditional cooperation parameters are `(p_CC, p_CD, p_DC, p_DD)`.
- A complete policy also needs a first-round cooperation probability `p0`, so one convenient representation is `(p0, p_CC, p_CD, p_DC, p_DD) in [0,1]^5`.
- Deterministic memory-one policies are the 32 vertices of this 5-dimensional hypercube.
- Tit-for-Tat corresponds to `(1, 1, 0, 1, 0)` under this ordering.
- Memory-one is a deliberate simplification: it gains interpretability and tractable search at the cost of strategies requiring longer history or internal state.
- Mutation can be viewed as a local perturbation of policy parameters. If Gaussian/additive perturbations are used, parameter bounds must be handled, e.g. by clipping; clipping itself can alter behaviour near 0 and 1.
- Evolution requires a fitness definition and a selection rule. Fitness in games is frequency-dependent: the value of a policy depends on the opponent population.
- AllD invades AllC, but reciprocal strategies such as TFT can limit exploitation after the first round and sustain cooperation with one another.
- With continuation probability `delta`, larger `delta` makes future consequences matter more and can make cooperation easier to sustain.
- A finite, commonly known last round enables backward induction; an uncertain horizon removes the known terminal round.
- Policy and environment are separate objects: changing the termination rule changes the environment, not necessarily the agent policy.

## Key idea in my own words

EXP-001 is not about hard-coding a good strategy. We define a constrained strategy space, an interaction environment, a fitness rule, and evolutionary operators, then observe which behaviours become common under those rules.

## What I can now derive or implement without help

Jonathan should be able to:

- read and interpret a memory-one policy vector;
- map deterministic strategies such as AllC, AllD and TFT to policy parameters;
- explain why `T > R > P > S` produces the Prisoner's Dilemma tension;
- explain the role of `2R > T + S`;
- reason through TFT versus AllC/AllD round sequences;
- distinguish policy from environment;
- explain why fitness can depend on population composition;
- implement bounded mutation conceptually once the implementation interface is specified.

## What is still unclear

The following are design choices rather than prerequisites for more conceptual questioning:

- exact research question and hypothesis for EXP-001;
- whether `p0` evolves or is fixed globally;
- finite-horizon versus stochastic-continuation environment for the first implementation;
- exact fitness aggregation across opponents;
- exact selection operator;
- mutation distribution and scale;
- population size, game length/generations, seed count, and stopping criteria;
- which baselines and metrics are required for the first run.

## Misconception or mistake discovered

- Conditional probabilities such as `p_CD` are probabilities of cooperating now given the previous state, not strategy labels.
- A memory-one policy does use history, but only the last round; it does not imply full-history optimisation.
- For deterministic policies with five binary parameters, the count is `2^5 = 32`, not `5^5`.
- Additive mutation can leave `[0,1]`; clipping is one simple boundary treatment.
- Larger continuation probability `delta` makes future rewards matter more, not less.
- In a known final round, individual payoff maximisation implies defection, not cooperation.
- Changing the horizon/termination rule changes the environment; changing TFT to AllD changes the policy.

## Connections to previous concepts

- Policy representation is a precursor to state/action/policy abstractions in reinforcement learning.
- Continuation probability is closely related to discounting and intertemporal incentives.
- Mutation scale anticipates exploration/exploitation trade-offs.
- Frequency-dependent fitness anticipates multi-agent learning and non-stationarity.
- Restricting to memory-one illustrates the general representation-capacity versus tractability trade-off.

## Questions to revisit later

- Does clipping create measurable boundary bias in evolved populations?
- When does evolving `p0` materially change the dynamics?
- How robust are cooperative regimes to noise or implementation error?
- Can multiple qualitatively different strategies coexist under frequency-dependent selection?

## Suggested retrieval check

1. Explain why a memory-one policy is an *entire strategy space* rather than one strategy, and state what information it discards.
2. Explain why AllD can invade AllC but may fail to dominate a reciprocal population over long repeated interactions.

## Pedagogical note

The Socratic sequence was useful initially but became too granular and detached from visible application. Future sessions should use questions selectively, bundle concepts into finite gates, and move promptly from understanding to experiment design and Jonathan-authored implementation. Do not turn EXP-001 into an open-ended quiz.