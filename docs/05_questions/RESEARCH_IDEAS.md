# RESEARCH_IDEAS.md

This file is a parking area for promising future directions. Inclusion here does **not** mean an idea belongs in the current stage.

The roadmap and charter take precedence.

## R-001 — Evolutionary repeated games

Explore how cooperation changes under:

- action noise;
- observation noise;
- mutation rate;
- different selection pressure;
- finite vs longer memory;
- spatial/network population structure;
- deliberate introduction of known fixed strategies;
- changing payoff matrices.

## R-002 — Evolution plus within-lifetime learning

Compare agents whose strategies evolve only across generations against agents that can also adapt during their own lifetime.

Investigate connections to the Baldwin effect and evolutionary assimilation.

## R-003 — Self-play league

Build a small game system with:

- current policy;
- historical checkpoints;
- opponent sampling;
- round-robin evaluation;
- exploitability/robustness metrics;
- visualisation of non-transitive matchups.

## R-004 — Emergent communication environment

Create a cooperative task where agents may send symbols with no predefined semantics.

Study whether communication is:

- informative about hidden state;
- causally useful for the receiving agent;
- robust when partners change;
- compositional or merely memorised.

## R-005 — Artificial Discovery Lab

Create a reusable framework that compares search mechanisms on small, objectively verifiable optimisation/discovery tasks.

Candidate problem families:

- bin packing;
- graph colouring;
- scheduling;
- knapsack variants;
- symbolic regression;
- small program synthesis tasks.

Compare:

- random search;
- hill climbing;
- genetic/evolutionary search;
- quality-diversity methods;
- learned proposal mechanisms;
- later, LLM-guided generation.

## R-006 — Generator vs verifier study

Hold the evaluator fixed while varying candidate generators.

Research question:

> How much of automated discovery performance comes from generator quality versus search/archive/evaluation design?

## R-007 — Adversarial evaluator exploitation

Construct deliberately imperfect objective functions and observe whether search discovers pathological high-scoring solutions.

Use this to study reward hacking and specification gaming in a controlled setting.

## R-008 — Finance-domain discovery experiments

Only after the general mechanisms are understood, explore whether the laboratory can search for:

- portfolio heuristics;
- execution/scheduling policies;
- robust hedging strategies in toy markets;
- algorithmic approximations for small pricing/risk subproblems.

Do not begin here simply because the domain is familiar; use finance later as a demanding application domain for mechanisms already understood.
