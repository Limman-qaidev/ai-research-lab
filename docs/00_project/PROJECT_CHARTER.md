# PROJECT_CHARTER.md

**Project:** Artificial Intelligence Research Lab  
**Owner:** Jonathan  
**Version:** 0.2  
**Status:** RATIFIED  
**Created:** 2026-09-06  
**Ratified:** 2026-09-06

## 1. Mission

Build a long-term personal research laboratory for learning artificial intelligence from first principles and for progressively developing the ability to recognise, formulate, investigate, and solve problems using systems in which agents, models, learning, search, evolution, competition, cooperation, and evaluation interact.

The project exists to move beyond superficial use of AI tools toward scientific and technical independence: Jonathan should become capable not only of using known methods, but of recognising when a mechanism is appropriate, explaining why it works, reproducing research, adapting it to new domains, and eventually designing new experiments, techniques, systems, or hypotheses.

A long-term aspiration is to become capable of identifying problems that are amenable to AI-based approaches even before the solution method is obvious, and to develop original solutions that may not have been previously considered.

## 2. Core research question

How can we design artificial systems in which adaptive components interact — competitively, cooperatively, evolutionarily, or through search — so that useful strategies, behaviours, algorithms, hypotheses, or other solutions emerge from the system rather than being manually specified in advance?

A second, equally important question is:

How can Jonathan develop enough conceptual and mathematical understanding to recognise which mechanism is appropriate for a new problem, justify that choice, and design a rigorous experiment to test it?

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

The project is intentionally domain-general. Finance and markets are natural application areas because of Jonathan's professional background, but they are not the defining purpose of the laboratory. The same mechanisms should be explored wherever they are scientifically or technically interesting: markets, algorithmic problems, games, scientific questions, simulations, optimisation problems, or future domains not yet identified.

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
9. explain when it should not be used;
10. recognise when the underlying mechanism may transfer to a problem in another domain.

The target is not encyclopaedic coverage. It is the development of a durable internal model of the field that supports independent reasoning.

## 5. Capability milestones

### Approximately 6 months

The project should be considered on track if Jonathan can take a well-scoped problem and:

- identify one or more plausible AI mechanisms that could address it;
- explain why those mechanisms are relevant;
- state their assumptions and limitations;
- distinguish a mechanism from the library or framework that implements it;
- design a small experiment capable of testing whether the proposed mechanism is useful.

The success criterion is therefore capability, not completion of a list of topics.

### Approximately 2 years

A major long-term milestone is that Jonathan can take a novel or poorly structured problem and:

- formulate a rigorous research question;
- connect the problem to relevant mathematical or algorithmic structures;
- navigate and understand the relevant literature;
- reconstruct the motivation and derivations behind a research paper;
- reproduce and critically test published work;
- adapt known methods to a new domain;
- design an original experiment, method, system, or technique when existing approaches are insufficient;
- explain why a chosen approach is preferable to plausible alternatives.

The aspiration is not merely to consume research, but eventually to contribute original technical ideas and to be capable of leading technical discussions in these areas.

## 6. Long-term technical scope

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

## 7. Non-goals

Unless this charter is explicitly amended, the project is **not primarily** about:

- building wrappers around commercial LLM APIs;
- creating generic “researcher / critic / manager” prompt-agent teams;
- learning whichever orchestration framework is currently fashionable;
- rapidly shipping an AI SaaS product;
- maximising code volume;
- copying reference implementations without understanding them;
- treating an LLM judge as equivalent to an objective verifier;
- collecting tutorials, papers, or notebooks without integrating the knowledge;
- becoming dependent on AI-generated code for mechanisms Jonathan should understand and be able to implement himself;
- using a model merely because it is powerful or fashionable without understanding what it does, why it is appropriate, and what alternatives exist.

These tools may appear later if they solve a well-defined problem in the laboratory.

## 8. Core design principles

### 8.1 Mechanism before framework

Understand the mechanism before delegating it to a high-level library.

### 8.2 Evaluation is first-class

A powerful generator is insufficient without a meaningful evaluation process. Special attention will be paid to reward design, fitness functions, objective metrics, verifiers, exploitability, robustness, and unintended optimisation targets.

### 8.3 Search is part of intelligence

The project will not reduce AI to representation learning. Search, planning, optimisation, population dynamics, and interaction are treated as central mechanisms.

### 8.4 Emergence must be demonstrated, not asserted

Claims that cooperation, strategy, communication, or discovery has “emerged” must be supported by reproducible measurements and appropriate baselines.

### 8.5 Scientific discipline over demos

Experiments should be hypothesis-driven where practical, reproducible, multi-seed when stochastic, and explicit about limitations.

### 8.6 Simplicity before scale

Prefer small environments where every mechanism can be understood and inspected before scaling to neural networks, distributed training, large models, or expensive compute.

### 8.7 LLMs are components, not oracles

When foundation models are eventually introduced, their role should be explicit: generator, mutator, critic, planner, encoder, etc. Correctness should be delegated to external verification whenever the problem permits it.

### 8.8 AI should amplify, not replace, technical capability

The assistant and other AI tools should increase Jonathan's learning rate, access to literature, ability to test ideas, and quality of criticism without displacing the cognitive work that the project exists to develop.

A successful use of AI in this laboratory should make Jonathan more capable of reasoning and programming independently over time, not less capable.

### 8.9 Foundations are the spine; applications are laboratories

The project will study mechanisms in a domain-general way and then apply them to concrete problems. Markets and finance may provide important experiments, but no application domain should distort the underlying learning path.

The intended pattern is:

`fundamental mechanism -> minimal experiment -> understanding -> application to a real problem -> critique and generalisation`

## 9. Initial learning trajectory

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

## 10. Anti-drift test

Before a substantial new direction, ask:

1. Does this deepen understanding of a mechanism relevant to the core research question?
2. Can we state what problem it solves?
3. Is the prerequisite knowledge in place?
4. Is there a simpler experiment that would teach the mechanism better?
5. Are we learning or merely integrating a tool?
6. Will this move us toward systems capable of adaptive interaction or automated discovery?
7. Does this strengthen or weaken Jonathan's independent ability to reason and implement?
8. Are we choosing the mechanism because it fits the problem, or because it is currently fashionable?

If the answer is mostly no, the work probably does not belong in the current project.

## 11. Ratification

This charter was explicitly ratified by Jonathan on 2026-09-06 after reviewing and refining the mission, capability milestones, non-goals, and pedagogical intent.

Future amendments are allowed, but material changes should be deliberate, documented, and checked against the anti-drift principles above.
