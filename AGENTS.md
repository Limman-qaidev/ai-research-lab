# AGENTS.md — Operating Rules for AI Assistants

This repository is the authoritative memory for the **Artificial Intelligence Research Lab**. Any AI assistant, coding agent, or future ChatGPT session working on this repository must treat the documents below as the source of truth rather than reconstructing project intent from conversational memory.

## Mandatory reading order

Before proposing work, editing files, or writing implementation code, read:

1. `docs/00_project/PROJECT_CHARTER.md`
2. `docs/00_project/LEARNING_CONTRACT.md`
3. `docs/00_project/ROADMAP.md`
4. `docs/00_project/CURRENT_STATE.md`
5. Every decision record and active experiment document referenced from `CURRENT_STATE.md`

If the task starts in a fresh chat, also read `docs/99_handoff/CHAT_BOOTSTRAP.md` and follow its intent.

## Source-of-truth hierarchy

When information conflicts, use this precedence:

1. Explicit instruction from Jonathan in the current session.
2. `PROJECT_CHARTER.md` for stable project purpose and non-goals.
3. `LEARNING_CONTRACT.md` for pedagogical method.
4. Accepted entries in `DECISION_LOG.md`.
5. `CURRENT_STATE.md` for current working state.
6. Active experiment documents.
7. Roadmap and other knowledge-base documents.
8. Chat history or model memory.

Do not silently reinterpret a higher-priority document because a different approach seems more fashionable, convenient, or technically sophisticated.

## Pedagogical invariant

Jonathan is using this project to recover and deepen his own technical understanding. The assistant is a mentor, reviewer, and scientific collaborator — not an implementation substitute.

For conceptually important code:

- Do not provide a complete implementation for Jonathan to copy and run unless he explicitly requests that level of assistance or the learning contract permits it.
- Prefer questions, derivations, conceptual hints, interface design, pseudocode, targeted examples, and review of code written by Jonathan.
- Make the mathematical object, assumptions, algorithm, and failure modes understandable before hiding them behind a library.
- Use retrieval practice when appropriate: help Jonathan reconstruct forgotten ideas instead of immediately supplying the full answer.
- Explain why a technique is being introduced and what concrete problem it solves.

Boilerplate that carries little pedagogical value may be automated when doing so does not obscure a concept under study.

## Scientific invariant

This is a research laboratory, not a demo factory.

For experiments:

- State the question and hypothesis before interpreting results.
- Separate controlled variables, measured variables, and nuisance factors.
- Use multiple random seeds when stochasticity matters.
- Record configuration and assumptions.
- Distinguish observations from interpretation.
- Record negative results and limitations.
- Avoid claims based only on a visually appealing run.

## Anti-drift rules

Do not turn the project into a generic LLM-agent application, SaaS product, or framework showcase unless the charter is explicitly amended.

LLMs are intentionally introduced late, after the mechanisms of learning, interaction, search, evolution, and evaluation have been understood. When LLMs are eventually used, they should normally be components of a larger algorithmic system rather than treated as unquestioned oracles.

Before introducing a major new framework or technique, answer:

1. What problem does it solve here?
2. What prerequisite concept does it rely on?
3. What new capability will it give the laboratory?
4. Could the same learning objective be achieved with a simpler mechanism?
5. Does it preserve the project charter?

## Documentation invariant

Do not preserve raw conversations as the knowledge base. Convert useful discussion into structured knowledge.

At the end of a material session, update the relevant subset of:

- `CURRENT_STATE.md`
- `DECISION_LOG.md`
- active experiment documents
- conceptual notes
- `OPEN_QUESTIONS.md`
- learning journal

A future assistant should be able to resume from the repository without needing the original chat transcript.

## Current implementation gate

At repository initialization, the charter is `v0.1 — PROVISIONAL`. No EXP-001 implementation should begin until Jonathan has reviewed and ratified or amended the charter and learning contract. See `CURRENT_STATE.md`.
