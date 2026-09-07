# CURRENT_STATE.md

**Last updated:** 2026-09-07  
**Project phase:** Stage 0B — EXP-001 experiment design  
**Implementation status:** NOT STARTED  
**Previous gate:** COMPLETED — core Iterated Prisoner's Dilemma foundations covered  
**Next task:** close a finite set of EXP-001 design choices, then Jonathan implements the minimal repeated-game simulator

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise which mechanisms may address a new problem, explain why they work, reproduce and critique research, adapt methods across domains, and eventually design original experiments, systems, techniques, or hypotheses.

The laboratory is domain-general. Finance may provide future applications, but it is not the organising principle.

`PROJECT_CHARTER.md` v0.2 and `LEARNING_CONTRACT.md` v0.2 were explicitly ratified by Jonathan on 2026-09-06.

## 2. Pedagogical invariant

The expected working sequence remains:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`

The assistant should not default to complete generated implementations for core learning mechanisms. AI should amplify Jonathan's reasoning and programming capability, not replace it.

### Important pedagogical adjustment from 2026-09-07

The long Socratic sequence on Prisoner's Dilemma foundations became too granular and Jonathan reported disengagement because the application path was no longer visible.

Future teaching should therefore:

- preserve Socratic reasoning where it tests a genuinely important concept;
- avoid endless chains of micro-questions;
- define finite conceptual gates;
- state explicitly what the current question will be used for;
- move promptly from understanding to design and Jonathan-authored implementation;
- persist material progress so a chat boundary never forces a restart.

This is an adjustment in pacing, not a relaxation of the learning contract.

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

## 4. Active experiment — EXP-001

**Working title:** Evolutionary Iterated Prisoner's Dilemma  
**Experiment document:** `docs/03_experiments/EXP-001_EVOLUTIONARY_IPD.md`  
**Status:** DESIGN — NO IMPLEMENTATION YET

### Core concepts now covered

Jonathan has worked through:

1. one-shot Prisoner's Dilemma and strict dominance of defection;
2. meaning of `T, R, P, S` and why `T > R > P > S` creates the strategic tension;
3. role of `2R > T + S` in repeated interaction;
4. why repetition introduces intertemporal incentives;
5. history-conditioned strategies and Tit-for-Tat;
6. stochastic memory-one policies;
7. complete policy representation `(p0, p_CC, p_CD, p_DC, p_DD)`;
8. deterministic versus stochastic policies and the `2^5 = 32` deterministic vertices;
9. memory-one versus full-history expressivity/tractability;
10. local mutation, parameter bounds, and clipping;
11. fitness and the distinction between threshold and probabilistic selection;
12. frequency-dependent fitness;
13. AllC, AllD and TFT interaction logic;
14. interaction horizon, continuation probability `delta`, and future incentives;
15. known finite horizon versus uncertain continuation;
16. separation between policy and environment.

The detailed learning record and misconceptions to revisit are in `docs/04_learning_journal/2026-09-07_EXP-001_IPD_foundations.md`.

## 5. Immediate finite design gate

Do not return to an open-ended conceptual quiz. The next step is to make and justify a small set of design choices for the first executable version of EXP-001:

1. exact first research question / primary hypothesis;
2. fixed finite horizon versus stochastic continuation for v1;
3. whether `p0` evolves or is fixed;
4. fitness evaluation scheme across opponents;
5. selection operator;
6. mutation distribution, scale concept, and boundary handling;
7. minimal deterministic baseline tests and metrics.

After this gate, implementation begins.

## 6. First implementation milestone

Jonathan should write the core code himself.

The first milestone is intentionally small:

- represent a policy;
- simulate a repeated game between two policies;
- accumulate/average payoffs;
- correctly handle first-round behaviour and `CC/CD/DC/DD` state ordering;
- test deterministic reference matchups: AllC/AllC, AllD/AllC, TFT/AllC, TFT/AllD, TFT/TFT.

Do **not** implement population evolution until these invariants are tested.

The assistant may provide interfaces, pseudocode, invariants, mathematical checks, and code review, but should not supply the complete core implementation unless Jonathan explicitly asks.

## 7. What has NOT been done

- no Python package structure has been created;
- no environment or policy classes exist;
- no evolutionary algorithm has been implemented;
- no experimental hyperparameters are ratified;
- no numerical experiment has been run;
- no RL or LLM framework has been selected.

This remains intentional.

## 8. Resume instruction

A future assistant should read `EXP-001_EVOLUTIONARY_IPD.md` and the 2026-09-07 learning journal entry, then continue with the **finite experiment-design gate** above.

Do not restart the Prisoner's Dilemma foundations from zero. Do not ask a long chain of recall questions before application. Once the seven design choices are sufficiently justified, move to Jonathan-authored minimal simulator implementation and review.