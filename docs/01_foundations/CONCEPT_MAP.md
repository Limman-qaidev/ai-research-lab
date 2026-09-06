# CONCEPT_MAP.md

This file is a living map of how the major ideas in the laboratory connect. It is not intended to be exhaustive or polished at the beginning.

## Current map

```text
ARTIFICIAL INTELLIGENCE SYSTEMS
│
├── Decision-making under uncertainty
│   │
│   ├── Markov Decision Processes
│   │   ├── state
│   │   ├── action
│   │   ├── transition dynamics
│   │   ├── reward
│   │   ├── return
│   │   ├── value functions
│   │   └── Bellman equations
│   │
│   └── Reinforcement Learning
│       ├── Monte Carlo
│       ├── Temporal Difference
│       ├── SARSA
│       ├── Q-learning
│       ├── policy gradients
│       └── actor-critic / PPO
│
├── Strategic interaction
│   │
│   ├── Game Theory
│   │   ├── normal-form games
│   │   ├── repeated games
│   │   ├── best response
│   │   ├── Nash equilibrium
│   │   └── non-transitivity
│   │
│   ├── Stochastic / Markov Games
│   │   └── Multi-Agent Reinforcement Learning
│   │       ├── cooperative
│   │       ├── competitive
│   │       ├── mixed incentives
│   │       ├── non-stationarity
│   │       └── centralised training / decentralised execution
│   │
│   └── Self-play
│       ├── current opponent
│       ├── historical opponent pools
│       ├── league training
│       └── exploitability / robustness
│
├── Evolution and populations
│   │
│   ├── fitness
│   ├── selection
│   ├── mutation
│   ├── recombination
│   ├── replicator dynamics
│   ├── coevolution
│   ├── novelty search
│   ├── quality-diversity
│   └── MAP-Elites
│
├── Search and planning
│   │
│   ├── minimax
│   ├── alpha-beta pruning
│   ├── best-first / beam search
│   └── Monte Carlo Tree Search
│
├── Automated discovery
│   │
│   ├── candidate representation
│   ├── search space
│   ├── generator / mutation operator
│   ├── objective evaluator
│   ├── verifier
│   ├── archive
│   ├── program / heuristic search
│   └── symbolic / constrained search
│
└── Foundation models inside larger systems
    │
    ├── proposal generator
    ├── mutation operator
    ├── critic
    ├── planner
    └── representation model
        │
        └── must be separated conceptually from objective verification
```

## Founding connections to preserve

### Evolutionary games -> self-play

Both involve adaptation under strategic pressure from other policies. The key conceptual difference to investigate is whether adaptation occurs across generations/populations, within an agent through learning, or both.

### Game theory -> MARL

A multi-agent learning environment is not simply several independent MDPs. Other agents’ changing policies affect the effective dynamics seen by each learner.

### Search -> automated discovery

A discovery system can often be framed as:

`representation + candidate-generation/search + evaluation + selection/archive`

### LLMs -> automated discovery

An LLM can be a proposal mechanism without being the authority that determines correctness. This generator/verifier separation is a central long-term idea for the project.

## Current focus

Stage 0B will initially deepen the branch:

`Game Theory <-> Evolution and populations`

through EXP-001.
