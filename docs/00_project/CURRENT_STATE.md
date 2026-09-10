# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0D — EXP-001 population evaluation and evolutionary dynamics  
**Implementation status:** MINIMAL SIMULATOR VALIDATED  
**Previous gate:** COMPLETED — deterministic repeated-game baseline tests passed  
**Next task:** design and implement population-level fitness evaluation before selection and mutation

## 1. Stable project purpose

The repository is the authoritative memory of the Artificial Intelligence Research Lab.

Jonathan's long-term objective is to develop scientific and technical independence in AI: recognise which mechanisms may address a new problem, explain why they work, reproduce and critique research, adapt methods across domains, and eventually design original experiments, systems, techniques, or hypotheses.

The laboratory is domain-general. Finance may provide future applications, but it is not the organising principle.

`PROJECT_CHARTER.md` v0.2 and `LEARNING_CONTRACT.md` v0.2 were explicitly ratified by Jonathan on 2026-09-06.

## 2. Pedagogical invariant

The expected working sequence remains:

`problem -> intuition -> formal model -> derivation -> algorithm -> design -> Jonathan implements -> assistant reviews -> experiment -> documentation`

The assistant should not default to complete generated implementations for core learning mechanisms. AI should amplify Jonathan's reasoning and programming capability, not replace it.

Socratic questioning should be used where it tests genuinely important concepts, but not as an open-ended chain of micro-questions. Material progress must be persisted so chat boundaries never force a restart.

## 3. Active experiment — EXP-001

**Working title:** Evolutionary Iterated Prisoner's Dilemma  
**Experiment document:** `docs/03_experiments/EXP-001_EVOLUTIONARY_IPD.md`

Core conceptual foundations and the minimal repeated-game simulator are now complete enough to begin population-level work.

## 4. Validated minimal simulator

Jonathan implemented a simulator containing:

- a stochastic memory-one `Policy` represented by `(p0, p_CC, p_CD, p_DC, p_DD)`;
- `cooperation_probability(previous_state)` with explicit `None/CC/CD/DC/DD` mapping;
- stochastic action sampling from `[p, 1-p]`;
- a Prisoner's Dilemma payoff lookup;
- player-relative previous states (`state_a = action_a + action_b`, `state_b = action_b + action_a`);
- repeated play for a fixed number of rounds;
- cumulative game scores.

For a 100-round horizon, the deterministic reference matchups were confirmed exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

These results validate the first-round behaviour, payoff mapping, repeated-state transition logic, and player-relative `CD/DC` ordering for the deterministic baselines.

## 5. Current gate — population fitness

The next milestone is **not yet full evolution**. First implement population-level evaluation correctly.

Target mechanism:

1. create a population of policies;
2. evaluate every unordered pair once (round-robin, no self-play for v1);
3. accumulate each individual's payoff across opponents;
4. convert accumulated payoff to mean payoff per round/opponent;
5. return a fitness vector aligned with the population indices.

For population size `N`, the number of pairwise games should be:

`N(N-1)/2`.

The v1 candidate fitness definition is:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

Important invariant: fitness is frequency-dependent because every candidate is evaluated against the current population composition.

Do not implement reproduction, mutation, generations, or plotting until this population fitness layer is independently checked.

## 6. Validation ideas for population evaluation

Before selection/mutation, use tiny populations with analytically predictable outcomes, for example:

- population `[AllC, AllC]`;
- population `[AllC, AllD]`;
- population `[AllC, AllC, AllD]`;
- population `[TFT, TFT, AllD]`.

Manually derive expected aggregate and mean fitness before running the code. This will test bookkeeping independently of the already-validated two-policy game engine.

## 7. Still outstanding

- confirm match state is reset/local for every pairwise game when a `Game` object is reused;
- align type annotations with actual return types;
- population representation is not yet implemented;
- round-robin evaluator is not yet implemented;
- selection operator is not yet implemented;
- mutation operator is not yet implemented;
- no evolutionary generations have been run;
- no experimental results exist yet.

## 8. Resume instruction

Resume at **population-level fitness evaluation**. Do not revisit basic Prisoner's Dilemma theory or the deterministic simulator unless a regression appears. Jonathan should implement the core population evaluator himself; the assistant should help derive invariants, review design/code, and only then move to selection and mutation.