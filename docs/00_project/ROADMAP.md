# ROADMAP.md

**Version:** 0.1  
**Status:** Provisional learning roadmap  
**Created:** 2026-09-06

This roadmap is organised around **capabilities to acquire**, not topics to consume. A stage is complete only when its completion gate is met.

## Stage 0A — Operating framework and epistemic setup

### Purpose

Create a durable system for learning, documentation, experiment tracking, decision tracking, and chat-to-chat continuity.

### Deliverables

- ratified `PROJECT_CHARTER.md`;
- ratified `LEARNING_CONTRACT.md`;
- current-state and handoff protocol;
- decision log;
- experiment template;
- learning journal template;
- concept map and open-question register.

### Completion gate

Jonathan can explain how the repository prevents loss of project context and conceptual drift, and the charter/learning contract are explicitly ratified.

---

## Stage 0B — Evolutionary games and population dynamics

### Purpose

Understand agents, strategies, payoffs, fitness, selection, mutation, strategic interaction, cooperation, exploitation, diversity, and non-transitivity **before** introducing gradient-based learning.

### Theory

- normal-form and repeated games;
- Prisoner’s Dilemma;
- stochastic memory-one strategies;
- expected payoff;
- fitness;
- selection mechanisms;
- mutation;
- replicator intuition/dynamics;
- coevolution;
- strategic diversity;
- non-transitive interactions.

### Main experiment

`EXP-001 — Evolutionary Iterated Prisoner’s Dilemma`

Initial provisional design:

- population of stochastic memory-one policies;
- policy parameters `theta = (p_CC, p_CD, p_DC, p_DD)`;
- pairwise iterated games;
- round-robin or controlled opponent sampling;
- selection plus Gaussian mutation;
- multiple generations and multiple random seeds;
- metrics for fitness, cooperation, diversity, and pairwise performance.

### Completion gate

Jonathan can:

- derive and explain the payoff structure;
- explain why one-shot dominance and repeated-game dynamics differ;
- define a memory-one stochastic policy mathematically;
- explain fitness, selection pressure, mutation, and diversity;
- implement the core evolutionary loop himself;
- design and interpret multi-seed experiments;
- explain at least one observed strategy/dynamic without anthropomorphic language.

---

## Stage 1 — Single-agent reinforcement learning

### Purpose

Understand learning from interaction in a stationary environment.

### Theory

- Markov property;
- MDPs;
- return and discounting;
- state-value and action-value functions;
- Bellman expectation and optimality equations;
- dynamic programming;
- Monte Carlo methods;
- temporal-difference learning;
- SARSA;
- Q-learning;
- exploration/exploitation.

### Build

Small GridWorld-style environments and tabular agents implemented from first principles.

### Completion gate

Jonathan can reconstruct Bellman equations, derive the tabular Q-learning update, implement it without an RL library, explain on-policy vs off-policy learning, and diagnose basic convergence/exploration failures.

---

## Stage 2 — Function approximation, deep RL, and self-play

### Purpose

Move from tables to learned function approximators and then study learning against changing opponents.

### Theory

- function approximation;
- neural policies/value functions;
- DQN;
- replay buffers;
- target networks;
- policy gradients;
- advantage estimation;
- actor-critic;
- PPO;
- self-play;
- opponent pools;
- catastrophic forgetting of opponents;
- exploitability and robustness.

### Build

Progress from small adversarial games to a Connect Four self-play system with historical opponents and tournament evaluation.

### Completion gate

Jonathan can explain what specific instability each major deep-RL mechanism addresses and can build a small self-play loop whose improvement is evaluated against more than its latest opponent.

---

## Stage 3 — Multi-agent reinforcement learning

### Purpose

Understand what changes when several learning agents jointly determine the environment dynamics.

### Theory

- stochastic/Markov games;
- partial observations;
- joint actions;
- non-stationarity;
- independent learners;
- cooperative vs competitive rewards;
- centralised training/decentralised execution;
- opponent modelling;
- MARL evaluation.

### Build

A small custom cooperative/competitive grid environment with multiple learning agents.

### Completion gate

Jonathan can formulate the environment as a stochastic game, explain why independent learners face non-stationarity, and demonstrate experimentally how reward structure changes learned behaviour.

---

## Stage 4 — Emergent communication and population methods

### Purpose

Study communication, population diversity, league training, coevolution, and strategic robustness.

### Theory

- learned communication channels;
- information content of messages;
- population-based training;
- league/opponent sampling;
- Elo and its limitations;
- non-transitivity;
- quality-diversity;
- novelty search;
- MAP-Elites;
- evolutionary strategies.

### Build

Multi-agent environments where communication symbols have no predefined semantics, plus population/league evaluation infrastructure.

### Completion gate

Jonathan can distinguish genuine informative communication from arbitrary correlated signalling and can measure population-level strategic diversity beyond a single scalar score.

---

## Stage 5 — Search and planning

### Purpose

Understand intelligence as search over future possibilities, not only learned representations.

### Theory

- minimax;
- alpha-beta pruning;
- best-first and beam search;
- Monte Carlo Tree Search;
- exploration bonuses;
- planning with learned value/policy functions.

### Build

Search-based agents and at least one hybrid search + learned-policy experiment.

### Completion gate

Jonathan can explain the bias/variance and computational trade-offs between explicit search and direct policy execution and can implement a minimal MCTS.

---

## Stage 6 — Automated discovery

### Purpose

Turn optimisation, algorithmic, or mathematical tasks into search problems with objective external evaluation.

### Theory

- search spaces;
- candidate representations;
- fitness/objective design;
- program synthesis/search;
- symbolic regression;
- heuristic search;
- verifiers;
- constraint solvers;
- benchmark design;
- overfitting to evaluators.

### Main direction

Build an `Artificial Discovery Lab` capable of comparing random search, evolutionary search, learned search, and hybrid methods on problems such as bin packing, graph colouring, scheduling, or symbolic regression.

### Completion gate

Jonathan can formulate a new discovery task as `representation + search + evaluation`, build an objective verifier, and demonstrate improvement over meaningful baselines without relying on subjective LLM judgement.

---

## Stage 7 — LLM-guided discovery

### Purpose

Introduce foundation models only after the surrounding search/evaluation machinery is understood.

### Theory

- LLMs as proposal distributions;
- code/heuristic mutation;
- iterative improvement;
- generator–critic–verifier separation;
- correlated model errors;
- test-time search;
- evaluation leakage;
- sandboxing and deterministic verification.

### Build

Replace or augment evolutionary mutation/generation with an LLM while retaining objective evaluation and an archive of candidates.

### Completion gate

Jonathan can state precisely what role the LLM plays, compare it against non-LLM generators, and show whether it improves search efficiency or solution quality under the same evaluator.

---

## Stage 8 — Open-ended and hybrid systems

### Purpose

Explore research questions that combine learning, evolution, search, multiple agents, automatically generated tasks, or adaptive evaluators.

This stage is intentionally open. It should be entered only after enough foundations exist to distinguish meaningful open-ended learning from an impressive-looking but poorly measured simulation.

---

## Roadmap governance

The roadmap may be changed. The charter should change much less often.

Any major roadmap change should be recorded in `docs/02_decisions/DECISION_LOG.md` with:

- reason;
- prerequisites;
- what is being deferred or removed;
- effect on the core mission.
