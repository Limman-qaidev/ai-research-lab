# DECISION_LOG.md

This file records durable project decisions and the reasoning behind them. Do not rewrite history silently. If a decision changes, add a superseding decision or explicitly mark the earlier one as superseded.

---

## DEC-001 — Repository is the project source of truth

**Date:** 2026-09-06  
**Status:** ACCEPTED

### Decision

The Git repository, not a ChatGPT conversation or model memory, is the authoritative persistent memory of the project.

### Reason

Long chats can lose context, hit limits, or drift conceptually. Future assistants must be able to reconstruct intent and state from versioned documents.

### Consequences

- material sessions should update the repository;
- raw chat history is not sufficient documentation;
- future sessions begin by reading the project documents in the order defined by `AGENTS.md`.

---

## DEC-002 — Optimise for Jonathan’s learning, not implementation throughput

**Date:** 2026-09-06  
**Status:** ACCEPTED

### Decision

For conceptually important work, Jonathan writes the first implementation. The AI assistant primarily teaches, derives, gives hints/pseudocode, reviews code, and helps design experiments.

### Reason

Jonathan explicitly wants to avoid becoming dependent on generated code and wants to rebuild durable technical recall and understanding.

### Consequences

- complete implementations are not the default;
- retrieval practice and derivation are preferred;
- generated boilerplate is allowed only when it does not replace the learning objective.

---

## DEC-003 — Bottom-up mechanisms before LLM multi-agent orchestration

**Date:** 2026-09-06  
**Status:** ACCEPTED

### Decision

Do not begin the project with prompt-based multi-agent frameworks. Study evolutionary interaction, RL, self-play, MARL, population methods, and search before introducing LLMs.

### Reason

The goal is to understand the mechanisms underneath multi-agent and discovery systems, not merely orchestrate pretrained chat models.

### Consequences

- no LangChain/CrewAI-style architecture is required at the start;
- LLMs are intentionally deferred to the automated-discovery stages;
- when LLMs appear, their algorithmic role must be explicit.

---

## DEC-004 — First planned experiment is evolutionary Iterated Prisoner’s Dilemma

**Date:** 2026-09-06  
**Status:** ACCEPTED AS DIRECTION; DESIGN NOT YET RATIFIED

### Decision

Use an evolutionary Iterated Prisoner’s Dilemma with stochastic memory-one policies as the first substantive experiment.

### Reason

It isolates strategic interaction, fitness, selection, mutation, cooperation, exploitation, diversity, and coevolution before adding neural networks or gradient-based learning.

### Alternatives considered

- Q-learning GridWorld first;
- Tic-Tac-Toe/Connect Four self-play first;
- immediate LLM multi-agent experiment.

### Why deferred

Those alternatives introduce other mechanisms before population dynamics and strategic interaction have been studied cleanly.

---

## DEC-005 — Experiments must be treated as scientific objects

**Date:** 2026-09-06  
**Status:** ACCEPTED

### Decision

Experiments should explicitly distinguish question, hypothesis, design, measured variables, random seeds, observations, interpretation, and limitations.

### Reason

A stochastic system can easily produce visually convincing anecdotes. The project aims to learn how to make defensible claims rather than impressive demos.

### Consequences

- multiple seeds where relevant;
- baselines and controls;
- negative results are documented;
- interpretation is kept separate from raw results.

---

## DEC-006 — No implementation before charter/learning-contract ratification

**Date:** 2026-09-06  
**Status:** COMPLETED

### Decision

Do not begin EXP-001 implementation until Jonathan has reviewed and explicitly ratified or amended `PROJECT_CHARTER.md` and `LEARNING_CONTRACT.md`.

### Reason

The framework is intended to prevent long-term conceptual drift. The founding purpose and learning method must therefore be owned by Jonathan rather than silently fixed by the assistant.

### Completion

Jonathan explicitly ratified `PROJECT_CHARTER.md` v0.2 and `LEARNING_CONTRACT.md` v0.2 on 2026-09-06. The Stage 0A gate is closed. The project may advance to formal EXP-001 design, but this does not imply that EXP-001 implementation should begin before its question, assumptions, game definition, policy representation, evolutionary mechanism, evaluation protocol, and knowledge objectives are understood and documented.
