# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0D — EXP-001 population evaluation and evolutionary dynamics  
**Implementation status:** MINIMAL SIMULATOR VALIDATED; POPULATION FITNESS IMPLEMENTED, VALIDATION PENDING  
**Previous gate:** COMPLETED — deterministic repeated-game baseline tests passed  
**Next task:** validate the round-robin population fitness layer, then implement fitness-proportional reproduction

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

Core conceptual foundations and the minimal repeated-game simulator are complete. Population-level evaluation is now implemented locally and awaiting analytical validation.

## 4. Validated minimal simulator

Jonathan implemented a simulator containing:

- a stochastic memory-one `Policy` represented by `(p0, p_CC, p_CD, p_DC, p_DD)`;
- `cooperation_probability(previous_state)` with explicit `None/CC/CD/DC/DD` mapping;
- stochastic action sampling from `[p, 1-p]`;
- a Prisoner's Dilemma payoff lookup;
- player-relative previous states (`state_a = action_a + action_b`, `state_b = action_b + action_a`);
- repeated play for a fixed number of rounds;
- cumulative game scores;
- reset of match state at the start of every `play_game` call.

For a 100-round horizon, the deterministic reference matchups were confirmed exactly:

- AllC vs AllC -> `(300, 300)`;
- AllD vs AllC -> `(500, 0)`;
- AllD vs AllD -> `(100, 100)`;
- TFT vs AllD -> `(99, 104)`;
- TFT vs TFT -> `(300, 300)`;
- TFT vs AllC -> `(300, 300)`.

These results validate the first-round behaviour, payoff mapping, repeated-state transition logic, and player-relative `CD/DC` ordering for the deterministic baselines.

## 5. Population fitness implementation

Jonathan has now implemented the v1 round-robin evaluator using a Python list of `Policy` objects.

Current logic:

1. let `N = len(policies)`;
2. iterate unordered pairs with `for i in range(N-1)` and `for j in range(i+1, N)`;
3. run one repeated game for each pair;
4. add the two resulting scores to the corresponding population indices;
5. compute fitness as `scores / (num_rounds * (N - 1))`.

This matches the intended v1 definition:

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

The implementation still reads each pairwise result from `Game.total_score` after `play_game`; returning the pairwise score directly from `play_game` would be a cleaner contract, but this is not a conceptual blocker for the current milestone.

Minor type annotations also remain to be cleaned up later (`Policy` construction currently accepts Python lists in examples although annotated as `np.ndarray`; payoff/score return annotations do not exactly match NumPy return types).

## 6. Immediate validation gate

Before selection, confirm the population evaluator against analytically known cases for `n = 100`:

- `[AllC, AllC, AllD]` -> fitness `(1.5, 1.5, 5.0)`;
- `[TFT, TFT, AllD]` -> fitness `(1.995, 1.995, 1.04)`.

Once both pass, population fitness is considered validated and the next mechanism is fitness-proportional reproduction.

## 7. Next mechanism after validation — selection

The first selection operator will be fitness-proportional reproduction:

`P(parent = i) = F_i / sum_j(F_j)`.

Parents will be sampled with replacement to form a new population of the same size. Mutation is a separate subsequent step; do not combine selection and mutation until parent sampling is independently understood and tested.

## 8. Still outstanding

- confirm the two analytical population-fitness cases above;
- implement selection / parent sampling;
- implement mutation;
- combine evaluation, selection, and mutation into generations;
- add reproducible random-number handling before stochastic experiments;
- align type annotations and improve `play_game` return contract;
- no evolutionary experiment has been run yet.

## 9. Resume instruction

Resume by validating the current population evaluator against the two analytical populations. If both pass, do not revisit the simulator or Prisoner's Dilemma foundations; move directly to fitness-proportional selection, then mutation, then generation dynamics.