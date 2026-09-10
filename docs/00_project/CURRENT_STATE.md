# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0C — EXP-001 minimal simulator implementation and validation  
**Implementation status:** IN PROGRESS  
**Previous gate:** COMPLETED — core IPD foundations and v1 design choices established  
**Next task:** validate the minimal repeated-game simulator with deterministic baseline tests before implementing population evolution

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise which mechanisms may address a new problem, explain why they work, reproduce and critique research, adapt methods across domains, and eventually design original experiments, systems, techniques, or hypotheses.

The laboratory is domain-general. Finance may provide future applications, but it is not the organising principle.

`PROJECT_CHARTER.md` v0.2 and `LEARNING_CONTRACT.md` v0.2 were explicitly ratified by Jonathan on 2026-09-06.

## 2. Pedagogical invariant

The expected working sequence remains:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`

The assistant should not default to complete generated implementations for core learning mechanisms. AI should amplify Jonathan's reasoning and programming capability, not replace it.

### Pacing adjustment

Socratic questioning should be used where it tests genuinely important concepts, but not as an open-ended chain of micro-questions. Material progress must be persisted so chat boundaries never force a restart.

## 3. Active experiment — EXP-001

**Working title:** Evolutionary Iterated Prisoner's Dilemma  
**Experiment document:** `docs/03_experiments/EXP-001_EVOLUTIONARY_IPD.md`

Core conceptual foundations are complete. The project has moved from design into the first implementation milestone.

## 4. Current implementation progress

Jonathan has written a local candidate minimal simulator containing:

- a `Policy` represented by `(p0, p_CC, p_CD, p_DC, p_DD)`;
- `cooperation_probability(previous_state)` with explicit `None/CC/CD/DC/DD` mapping;
- stochastic action sampling from `[p, 1-p]`;
- a Prisoner's Dilemma payoff lookup;
- player-relative previous states (`state_a = action_a + action_b`, `state_b = action_b + action_a`);
- repeated play for a fixed number of rounds;
- cumulative game scores.

The candidate should analytically produce `(99, 104)` for TFT versus AllD over 100 rounds.

### Review issue still open

The current `Game` object stores previous states and cumulative score as instance state. Reusing the same `Game` instance for a second match would therefore continue from the previous match unless the state is explicitly reset. Before population-level simulation, match state should be local to a single `play_game` execution or reliably reset at its start.

There are also minor type-contract mismatches (`payoff` and `score` annotations versus returned NumPy arrays) and `play_game` currently returns no score directly. These are cleanup items, not conceptual blockers.

## 5. Immediate validation gate

Do **not** implement population evolution yet.

First create deterministic baseline tests for a fixed horizon `n`:

- AllC vs AllC -> `(3n, 3n)`;
- AllD vs AllC -> `(5n, 0)`;
- TFT vs AllC -> `(3n, 3n)`;
- TFT vs AllD -> `(n-1, n+4)`;
- TFT vs TFT -> `(3n, 3n)`;
- optionally AllD vs AllD -> `(n, n)`.

For `n=100`, TFT vs AllD must equal `(99, 104)`.

After these invariants pass, the minimal repeated-game simulator milestone is complete and the next phase may introduce population evaluation, fitness, selection, and mutation.

## 6. What has NOT been done

- deterministic baseline tests have not yet been confirmed;
- no population container/evaluation loop has been implemented;
- no evolutionary selection operator has been implemented;
- no mutation operator has been implemented;
- no numerical evolutionary experiment has been run;
- no RL or LLM framework has been selected.

## 7. Resume instruction

Resume by reviewing and testing Jonathan's minimal `Policy` + `Game` implementation. Fix match-state lifecycle before reusing `Game` across pairings. Do not restart Prisoner's Dilemma theory and do not jump to population evolution until the deterministic matchups pass.