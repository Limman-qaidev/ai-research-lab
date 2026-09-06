# CURRENT_STATE.md

**Last updated:** 2026-09-06  
**Project phase:** Stage 0B — formal design of EXP-001  
**Implementation status:** NOT STARTED  
**Previous gate:** COMPLETED — charter and learning contract ratified  
**Next task:** understand and formally define the Iterated Prisoner's Dilemma before designing code

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise which mechanisms may address a new problem, explain why they work, reproduce and critique research, adapt methods across domains, and eventually design original experiments, systems, techniques, or hypotheses.

The laboratory is domain-general. Finance may provide future applications, but it is not the organising principle.

`PROJECT_CHARTER.md` v0.2 and `LEARNING_CONTRACT.md` v0.2 were explicitly ratified by Jonathan on 2026-09-06.

## 2. Pedagogical invariant

The expected working sequence is:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`

The assistant should not default to complete generated implementations for core learning mechanisms. AI should amplify Jonathan's reasoning and programming capability, not replace it.

## 3. Current technical direction

The learning spine remains:

1. evolutionary games and population dynamics;
2. single-agent reinforcement learning;
3. self-play;
4. multi-agent reinforcement learning;
5. population and league methods;
6. search and planning;
7. automated discovery;
8. LLM-guided discovery;
9. open-ended and hybrid systems.

The project deliberately begins below the LLM-orchestration layer.

## 4. Active experiment direction — EXP-001

**Working title:** Evolutionary Iterated Prisoner's Dilemma  
**Status:** FORMAL DESIGN / LEARNING — NO IMPLEMENTATION YET

### Why this comes first

The experiment isolates strategic interaction, policies, fitness, selection, mutation, cooperation, exploitation, population dynamics, diversity, and non-transitivity before introducing neural networks or gradient-based reinforcement learning.

### Provisional elements inherited from the founding discussion

- two actions: cooperate (`C`) and defect (`D`);
- candidate payoff values: `T=5, R=3, P=1, S=0`;
- stochastic memory-one policies as a candidate representation;
- population-based evaluation and evolutionary selection/mutation;
- multi-seed analysis and explicit population-level metrics.

These remain provisional until Jonathan understands and can justify them. They are not implementation requirements merely because they appear here.

## 5. Immediate learning task

Before designing classes, files, APIs, or code, Jonathan should understand from first principles:

1. what the one-shot Prisoner's Dilemma is;
2. why the inequalities `T > R > P > S` define its strategic tension;
3. why `2R > T + S` is commonly imposed in the iterated setting;
4. why repeating the game changes the strategic problem even though the stage-game payoffs are unchanged;
5. what information an agent/policy can condition on;
6. why a memory-one stochastic policy can be represented by conditional cooperation probabilities;
7. what is gained and lost by restricting the strategy space to memory-one policies.

Only after these ideas are understood should the formal EXP-001 research question, hypothesis, variables, and evaluation protocol be written.

## 6. What has NOT been done

- no Python package structure has been created;
- no environment or agent classes exist;
- no evolutionary algorithm has been implemented;
- no experimental hyperparameters are ratified;
- no numerical experiment has been run;
- no RL or LLM framework has been selected.

This is intentional.

## 7. Resume instruction

A future assistant should resume with the conceptual/formal design of EXP-001, beginning with the game-theoretic foundations above. Do not jump to code because the charter gate has closed. The learning contract still requires understanding before implementation.
