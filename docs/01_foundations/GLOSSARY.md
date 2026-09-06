# GLOSSARY.md

This glossary is intentionally concise. Definitions should become more precise as the corresponding concepts are formally studied.

## Agent

An entity that receives information about an environment and selects actions. An agent may contain a learned model, a fixed rule, a search algorithm, or combinations of these.

## Model

A parameterised or non-parameterised mapping used to represent a relationship, prediction, policy, value, dynamics, or other component. A model is not automatically an agent.

## Environment

The process with which an agent interacts. It determines observations/states, receives actions, evolves, and may return rewards or other feedback.

## Policy

A rule for selecting actions. In stochastic form, a policy may be written as `pi(a | s)` or `pi(a | o)`, giving a distribution over actions conditional on state or observation.

## Reward

A scalar feedback signal associated with an interaction step in reinforcement learning or related sequential-decision problems. A reward is not necessarily identical to the real-world objective the designer intended.

## Return

A cumulative function of future rewards, often discounted. In standard RL, a common form is the discounted sum of future rewards.

## Fitness

A score used by an evolutionary process to determine reproductive or selection success. Fitness may derive from game payoff, task performance, novelty, multiple objectives, or another criterion.

## Selection

A mechanism that changes which individuals, strategies, or candidates are more likely to contribute to the next population or search iteration.

## Mutation

A stochastic or directed modification of a candidate or policy used to create variation during evolutionary or search processes.

## Population

A collection of agents, policies, candidate solutions, or other individuals considered simultaneously by an evolutionary or population-based method.

## Coevolution

Evolution in which the fitness landscape faced by one individual or population changes because other evolving individuals or populations also change.

## Self-play

A learning or search process in which an agent improves by playing against itself, versions of itself, or a population/history of related policies.

## Multi-Agent Reinforcement Learning (MARL)

Reinforcement learning in environments where multiple decision-making agents jointly affect transitions and rewards. Their simultaneous learning can make the effective environment non-stationary from any single agent’s perspective.

## Search space

The set of candidates over which an algorithm searches. Its representation strongly affects what solutions can be found and how efficiently they can be explored.

## Evaluator

A mechanism that assigns performance information or a score to a candidate, policy, program, or hypothesis.

## Verifier

A stronger form of evaluator focused on checking whether specified constraints, correctness conditions, or properties actually hold. A deterministic test suite, theorem prover, simulator with hard validity constraints, or exact solver may act as a verifier.

## Self-play league / opponent pool

A collection of current and historical opponent policies used to avoid evaluating or training only against the latest version of an agent.

## Non-transitivity

A strategic relationship in which pairwise superiority does not define a simple total ranking, e.g. strategy A beats B, B beats C, while C beats A.

## Emergent behaviour

Behaviour that arises from interactions, learning, optimisation, or population dynamics rather than being directly hard-coded as the target strategy. The term should be used cautiously and supported by measurement.

## LLM-guided search

A search procedure in which a large language model helps generate, mutate, rank, critique, or otherwise propose candidates while a broader algorithm controls iteration and evaluation.
