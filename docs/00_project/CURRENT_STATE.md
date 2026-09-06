# CURRENT_STATE.md

**Last updated:** 2026-09-06  
**Project phase:** Stage 0A — operating framework and epistemic setup  
**Implementation status:** NOT STARTED  
**Next gate:** Jonathan reviews and ratifies/amends the charter and learning contract

## 1. Origin of the project

Jonathan wants to move beyond superficial use of LLMs and AI tooling. He is especially interested in systems where multiple artificial agents or models interact, compete, cooperate, evolve, search, or collectively discover solutions.

The motivating examples discussed include self-play systems, multi-agent learning, evolutionary search, program/heuristic discovery, and architectures in which a generator proposes candidates but an external evaluator or verifier decides what is actually good.

A central insight from the founding discussion is:

> Interesting AI systems are often not “one model answering one prompt”. They can be understood as combinations of representation, learning, interaction, search, optimisation, population dynamics, and evaluation.

Another central principle is:

> A weak or unreliable generator can still be useful when embedded inside a strong search-and-verification loop.

## 2. Pedagogical requirement

Jonathan explicitly does **not** want the assistant to generate finished code that he blindly copies and executes. He is concerned that excessive AI assistance is eroding recall and technical independence.

The project should therefore maximise Jonathan’s own reconstruction, derivation, implementation, and documentation of the important mechanisms.

The expected working pattern is:

`question -> theory -> derivation -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`

See `LEARNING_CONTRACT.md` for the authoritative rules.

## 3. Continuity requirement

Jonathan is concerned that long chats eventually lose fidelity, hit interaction limits, or drift away from the original idea. Therefore this repository, not any single chat, is the persistent project memory.

A new chat should be able to reconstruct the project by reading the repository in the order specified in `AGENTS.md` and by using `docs/99_handoff/CHAT_BOOTSTRAP.md`.

Important information must be transformed into durable project documentation rather than left only in conversation history.

## 4. Current technical direction

The agreed provisional learning sequence is:

1. evolutionary games and population dynamics;
2. single-agent reinforcement learning;
3. deep RL and self-play;
4. multi-agent reinforcement learning;
5. emergent communication and population methods;
6. search and planning;
7. automated discovery;
8. LLM-guided discovery;
9. open-ended/hybrid systems.

The deliberate choice is to **avoid starting with LLM multi-agent orchestration**. LLMs will be introduced later as components inside systems whose search, learning, and evaluation mechanisms are already understood.

## 5. Planned first experiment — EXP-001

**Title:** Evolutionary Iterated Prisoner’s Dilemma  
**Status:** DESIGN CONCEPT ONLY — no code should be written yet

### Why this experiment comes first

It allows Jonathan to study agents, policies, strategic interaction, fitness, selection, mutation, population dynamics, cooperation, exploitation, diversity, and non-transitivity without simultaneously introducing neural networks or gradient-based reinforcement learning.

### Provisional game

Each player chooses:

- `C`: cooperate;
- `D`: defect.

Provisional payoff matrix:

|            | Opponent C | Opponent D |
|------------|------------|------------|
| Self C     | (3, 3)     | (0, 5)     |
| Self D     | (5, 0)     | (1, 1)     |

So `T = 5`, `R = 3`, `P = 1`, `S = 0`, with `T > R > P > S` and `2R > T + S`.

### Provisional policy representation

Each agent is a stochastic memory-one policy:

`theta = (p_CC, p_CD, p_DC, p_DD)`

where each component is the probability of cooperating conditional on the previous round’s pair of actions.

The policy space is therefore `[0, 1]^4`.

### Provisional evolutionary loop

1. initialise a population of policies;
2. evaluate policies through repeated pairwise games;
3. compute fitness;
4. select parents;
5. mutate policy parameters, initially considering Gaussian mutation;
6. create the next generation;
7. repeat;
8. analyse population-level dynamics across multiple random seeds.

### Provisional experimental scale

The founding discussion suggested values such as:

- population size `N ≈ 100`;
- game length `T ≈ 200` rounds;
- generations `G ≈ 500`;
- at least 20 random seeds;
- round-robin evaluation initially;
- tournament or softmax selection as candidate mechanisms.

These are **not accepted constants**. They must be justified during experiment design rather than copied mechanically.

### Metrics proposed for study

- mean fitness by generation;
- maximum fitness;
- cooperation rate;
- policy-parameter distributions;
- strategic/population diversity;
- pairwise payoff/performance matrix;
- sensitivity to mutation and selection pressure.

Later extensions may include action noise, injected fixed strategies, changing population structure, learning within lifetime, and non-transitive strategy analysis.

## 6. Important decisions already made

See `docs/02_decisions/DECISION_LOG.md`. The current founding decisions are:

- the repository is the source of truth;
- the project prioritises durable understanding over generated implementation speed;
- LLM orchestration is not the starting point;
- evolutionary Iterated Prisoner’s Dilemma is the first planned experiment;
- experiments will be treated scientifically rather than as visually interesting demos;
- no implementation starts until the charter and learning contract are ratified.

## 7. What has NOT been done

As of this state:

- no Python package structure has been created;
- no environment interface has been implemented;
- no agent class has been implemented;
- no evolutionary code exists;
- no numerical experiment has been run;
- no high-level RL library has been selected;
- no LLM API/framework has been selected;
- no experimental hyperparameters have been accepted as final.

This is intentional.

## 8. Immediate next session

The next substantive session should **not start coding**.

It should review `PROJECT_CHARTER.md` with Jonathan. The assistant should help him answer, in his own words:

1. What does he ultimately want to become capable of doing?
2. What exactly does “understanding AI deeply” mean to him?
3. Which parts of the provisional mission feel wrong or incomplete?
4. What would make this project a success after approximately 6 months?
5. What would make it a success after approximately 2 years?
6. Which things must the project never drift into?

Then review `LEARNING_CONTRACT.md` and amend it if needed.

Only when Jonathan explicitly ratifies both documents should:

- their status be changed to `RATIFIED`;
- DEC-006 be updated/closed accordingly;
- Stage 0A be considered complete;
- formal design of EXP-001 begin.

## 9. Resume instruction for a future assistant

Do not infer that EXP-001 should be implemented merely because its provisional specification appears above. The implementation gate is still closed.

Resume by helping Jonathan ratify the project framework unless a later repository update supersedes this state.
