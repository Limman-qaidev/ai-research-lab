# CURRENT_STATE.md

**Last updated:** 2026-09-10  
**Project phase:** Stage 0D — EXP-001 population evaluation and evolutionary dynamics  
**Implementation status:** MINIMAL SIMULATOR VALIDATED; POPULATION FITNESS IMPLEMENTED; FITNESS-PROPORTIONAL SELECTION IMPLEMENTED; MUTATION IMPLEMENTED AND VALIDATED  
**Previous gate:** COMPLETED — mutation creates bounded offspring without altering parents  
**Next task:** combine evaluation, selection, and mutation into one complete generation step, then validate generation invariants before looping over generations

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

Core conceptual foundations and the minimal repeated-game simulator are complete. The project is now assembling the evolutionary loop from independently understood pieces.

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

Analytical validation targets are:

- `[AllC, AllC, AllD]` -> `(1.5, 1.5, 5.0)`;
- `[TFT, TFT, AllD]` -> `(1.995, 1.995, 1.04)`.

`play_game` now returns the pairwise total, although `evaluate_population` still reads `self.total_score`; using the returned value directly is a later cleanup, not a conceptual blocker.

## 6. Fitness-proportional selection

Jonathan implemented parent sampling with replacement using:

`q_i = F_i / sum_j(F_j)`

and NumPy weighted sampling to select `N` parents from the current population.

The selection operator permits:

- the same parent to be chosen multiple times;
- lower-fitness parents to reproduce occasionally;
- some parents to leave no descendants in a generation.

## 7. Mutation — implemented and validated

Jonathan implemented local Gaussian mutation as:

`epsilon_k ~ Normal(0, sigma^2)`

`pi_child = clip(pi_parent + epsilon, 0, 1)`.

The implementation:

- perturbs all five policy parameters;
- creates a new `Policy` for the child;
- leaves the selected parent unchanged;
- clips all child probabilities to `[0,1]`.

A direct test using TFT-like parent `(1,1,0,1,0)` and `sigma=0.1` produced an offspring such as:

`(0.99917545, 1.0, 0.0770555, 0.91572224, 0.0)`

while the parent remained exactly `(1,1,0,1,0)`. This confirms both object-identity safety and boundary clipping behaviour.

The experimental v1 mutation scale remains provisionally `sigma=0.05`; `sigma=0.1` was used only as an implementation check. Mutation-scale sensitivity should be studied later rather than assumed.

## 8. Next mechanism — one complete generation

Do not add plotting or long multi-generation runs yet.

Construct one generation as the composition:

1. `fitness = evaluate_population(population)`;
2. `parents = select_parents(population, fitness)`;
3. for each selected parent, create one new mutated `Policy`;
4. collect exactly `N` offspring as the next population.

Generation-level invariants to validate before looping:

- population size remains exactly `N`;
- every member of the next generation is a newly created `Policy`;
- every parameter remains in `[0,1]`;
- the original population is unchanged after reproduction/mutation;
- selection probabilities sum to 1;
- with `sigma=0`, offspring genotypes are exact copies of selected parents (but still distinct objects).

Only after this single-generation transformation is validated should the experiment introduce repeated generations and record population-level metrics.

## 9. Still outstanding

- explicitly confirm the analytical population-fitness outputs if not already recorded;
- combine evaluation, selection, and mutation into a validated generation step;
- initialize a genuinely random population in `[0,1]^5`;
- add reproducible random-number handling before scientific runs;
- define and record generation-level metrics;
- loop over multiple generations;
- align minor type annotations and reduce unnecessary hidden `Game` state later;
- no evolutionary experiment has been run yet.

## 10. Resume instruction

Resume at the **single-generation transformation**. Do not revisit Prisoner's Dilemma foundations, deterministic simulator tests, selection, or mutation unless a regression appears. Jonathan should implement the generation composition himself. Once its invariants pass, move to random population initialization, reproducible seeds, generation-level metrics, and only then the first multi-generation experiment.