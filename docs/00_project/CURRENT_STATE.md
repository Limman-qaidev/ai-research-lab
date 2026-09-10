# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0D — EXP-001 population evaluation and evolutionary dynamics  
**Implementation status:** MINIMAL SIMULATOR VALIDATED; POPULATION FITNESS IMPLEMENTED; FITNESS-PROPORTIONAL SELECTION IMPLEMENTED  
**Previous gate:** COMPLETED — deterministic repeated-game baseline tests passed  
**Next task:** implement mutation as creation of new offspring policies, then combine evaluation-selection-mutation into one generation

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

Core conceptual foundations and the minimal repeated-game simulator are complete. The project is now implementing the evolutionary loop in isolated layers.

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

## 5. Population fitness layer

Jonathan implemented round-robin evaluation using a Python list of `Policy` objects.

For population size `N`:

1. every unordered pair `(i,j)` is evaluated exactly once;
2. both payoffs from the repeated match are accumulated at their population indices;
3. fitness is normalized as

`F_i = total_payoff_i / ((N-1) * n_rounds)`.

Analytical validation targets remain:

- `[AllC, AllC, AllD]` -> `(1.5, 1.5, 5.0)`;
- `[TFT, TFT, AllD]` -> `(1.995, 1.995, 1.04)`.

The implementation now makes `play_game` return the pairwise total, although `evaluate_population` still reads `self.total_score`; using the returned value directly is a later cleanup, not a conceptual blocker.

## 6. Fitness-proportional selection

Jonathan implemented parent sampling with replacement using:

`q_i = F_i / sum_j(F_j)`

and NumPy weighted sampling to select `N` parents from the current population.

For the analytical fitness vector `(1.995, 1.995, 1.04)`, the corresponding reproduction probabilities are approximately:

`(0.3966, 0.3966, 0.2068)`.

This selection operator intentionally permits:

- the same parent to be chosen multiple times;
- lower-fitness parents to reproduce occasionally;
- some parents to leave no descendants in a generation.

### Important object-identity invariant for the next step

The selected parents are references to existing `Policy` objects. Mutation must **not** alter selected parent objects or their `probabilities` arrays in place.

Each offspring must be a newly constructed `Policy` whose parameters are derived from the selected parent's parameters. This prevents:

- mutating the previous generation retroactively;
- two offspring selected from the same parent unintentionally sharing a mutable parameter array;
- one child's mutation modifying a sibling or parent.

## 7. Next mechanism — mutation

Implement mutation separately before constructing full generations.

Candidate v1 mechanism:

`pi_child = clip(pi_parent + epsilon, 0, 1)`

with independent parameter perturbations

`epsilon_k ~ Normal(0, sigma^2)`.

Initial design value remains `sigma = 0.05`, subject to later sensitivity analysis.

Mutation should:

- operate on all five policy parameters;
- return a **new** `Policy`;
- leave the parent unchanged;
- keep every parameter in `[0,1]`;
- use clipping for the first implementation;
- later expose/measure clipping frequency because clipping may bias the boundaries.

Before population evolution, test mutation invariants independently.

## 8. Still outstanding

- explicitly confirm the analytical population-fitness outputs if not already recorded;
- implement and validate mutation;
- combine evaluation, selection, and mutation into generations;
- initialize a genuinely random population in `[0,1]^5`;
- add reproducible random-number handling before scientific runs;
- define and record generation-level metrics;
- align minor type annotations and remove unnecessary hidden state later;
- no evolutionary experiment has been run yet.

## 9. Resume instruction

Resume at **mutation**. Do not revisit basic Prisoner's Dilemma theory or the deterministic simulator unless a regression appears. Parent sampling is already implemented. The next conceptual/programming invariant is that offspring are new objects derived from parent genotypes, not in-place mutations of parent references. After mutation is validated, build a single generation step and only then loop over generations.