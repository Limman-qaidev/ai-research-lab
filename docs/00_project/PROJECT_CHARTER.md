# PROJECT_CHARTER.md

**Project:** Artificial Intelligence Research Lab  
**Owner:** Jonathan  
**Version:** 0.1  
**Status:** PROVISIONAL — must be reviewed and ratified before EXP-001 implementation  
**Created:** 2026-09-06

## 1. Mission

Build a long-term personal research laboratory for learning artificial intelligence from first principles and for progressively designing systems in which agents, models, learning, search, evolution, competition, cooperation, and evaluation interact to produce useful behaviour or discover solutions that were not explicitly programmed by a human.

The project exists to move beyond superficial use of AI tools and toward a deep understanding of the mechanisms that make intelligent systems work.

## 2. Core research question

How can we design artificial systems in which multiple adaptive components interact — competitively, cooperatively, evolutionarily, or through search — so that useful strategies, behaviours, algorithms, or hypotheses emerge from the system rather than being manually specified in advance?

## 3. Why this project exists

The motivating observation is that many important AI systems are not adequately described as a single pretrained model answering a prompt. They combine mechanisms such as:

- policies and environments;
- reinforcement learning;
- self-play;
- multi-agent interaction;
- population-based learning;
- evolutionary search;
- planning and tree search;
- external evaluators and verifiers;
- program or heuristic search;
- eventually, foundation models used as proposal mechanisms inside a larger system.

The project aims to understand these mechanisms individually and then combine them deliberately.

## 4. What “learning AI deeply” means in this project

A concept is not considered understood merely because Jonathan can recognise its name, call a library implementation, or reproduce an explanation.

For important concepts, understanding should progress toward the ability to:

1. explain what problem the method solves;
2. define its mathematical objects and assumptions;
3. derive or reconstruct its key equations;
4. explain the algorithm step by step;
5. implement the essential mechanism without relying on a black-box library;
6. design tests or experiments that reveal whether it works;
7. identify common failure modes and limitations;
8. connect it to earlier and later concepts in the project;
9. explain when it should not be used.

Not every concept must reach the same depth immediately, but the project should favour durable understanding over fast coverage.

## 5. Long-term technical scope

The intended scope includes, in roughly increasing order of abstraction:

- game theory and repeated games;
- evolutionary computation and coevolution;
- Markov decision processes;
- single-agent reinforcement learning;
- deep reinforcement learning;
- self-play;
- stochastic/Markov games;
- multi-agent reinforcement learning;
- emergent communication;
- population methods and league training;
- search and planning, including minimax and Monte Carlo Tree Search;
- quality-diversity and open-ended search;
- automated algorithm, heuristic, or program discovery;
- symbolic and constrained verification where useful;
- LLM-guided generation, mutation, critique, or search;
- hybrid systems combining learned models with deterministic evaluators or solvers.

The roadmap may evolve, but additions must preserve the mission.

## 6. Non-goals

Unless this charter is explicitly amended, the project is **not primarily** about:

- building wrappers around commercial LLM APIs;
- creating generic “researcher / critic / manager” prompt-agent teams;
- learning whichever orchestration framework is currently fashionable;
- rapidly shipping an AI SaaS product;
- maximising code volume;
- copying reference implementations without understanding them;
- treating an LLM judge as equivalent to an objective verifier;
- collecting tutorials, papers, or notebooks without integrating the knowledge.

These tools may appear later if they solve a well-defined problem in the laboratory.

## 7. Core design principles

### 7.1 Mechanism before framework

Understand the mechanism before delegating it to a high-level library.

### 7.2 Evaluation is first-class

A powerful generator is insufficient without a meaningful evaluation process. Special attention will be paid to reward design, fitness functions, objective metrics, verifiers, exploitability, robustness, and unintended optimisation targets.

### 7.3 Search is part of intelligence

The project will not reduce AI to representation learning. Search, planning, optimisation, population dynamics, and interaction are treated as central mechanisms.

### 7.4 Emergence must be demonstrated, not asserted

Claims that cooperation, strategy, communication, or discovery has “emerged” must be supported by reproducible measurements and appropriate baselines.

### 7.5 Scientific discipline over demos

Experiments should be hypothesis-driven where practical, reproducible, multi-seed when stochastic, and explicit about limitations.

### 7.6 Simplicity before scale

Prefer small environments where every mechanism can be understood and inspected before scaling to neural networks, distributed training, large models, or expensive compute.

### 7.7 LLMs are components, not oracles

When foundation models are eventually introduced, their role should be explicit: generator, mutator, critic, planner, encoder, etc. Correctness should be delegated to external verification whenever the problem permits it.

## 8. Initial learning trajectory

The provisional trajectory is:

1. evolutionary games and population dynamics;
2. single-agent reinforcement learning;
3. self-play;
4. multi-agent reinforcement learning;
5. population and league methods;
6. search and planning;
7. automated discovery;
8. LLM-guided discovery;
9. open-ended and hybrid systems.

The first planned scientific experiment is an evolutionary Iterated Prisoner’s Dilemma using memory-one stochastic policies. Its purpose is to study population dynamics, fitness, selection, mutation, cooperation, exploitation, and strategic diversity before introducing gradient-based learning.

## 9. Anti-drift test

Before a substantial new direction, ask:

1. Does this deepen understanding of a mechanism relevant to the core research question?
2. Can we state what problem it solves?
3. Is the prerequisite knowledge in place?
4. Is there a simpler experiment that would teach the mechanism better?
5. Are we learning or merely integrating a tool?
6. Will this move us toward systems capable of adaptive interaction or automated discovery?

If the answer is mostly no, the work probably does not belong in the current project.

## 10. Ratification gate

This document was reconstructed from the founding conversation so that project continuity is not lost. It is intentionally marked provisional.

Before implementation begins, Jonathan should review it and answer at minimum:

- What is missing from the mission?
- What wording does not reflect his actual motivation?
- Which non-goals are too restrictive or too weak?
- What would count, personally, as success after 6 months and after 2 years?
- Which aspects of “deep understanding” matter most to him?

Only after that review should the status change from `PROVISIONAL` to `RATIFIED`.
