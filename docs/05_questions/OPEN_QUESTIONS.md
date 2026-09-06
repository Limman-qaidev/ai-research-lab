# OPEN_QUESTIONS.md

This register preserves questions that should not be lost merely because they were not answered in the session where they arose.

Use statuses: `OPEN`, `PARTIALLY ANSWERED`, `ANSWERED`, `DEFERRED`.

---

## Q-001 — What exactly qualifies as an agent?

**Status:** OPEN  
**Origin:** Founding discussion

Where should we draw the boundary between a model, an optimisation algorithm, a policy, and an agent? Which properties are essential versus conventional?

---

## Q-002 — Why can cooperation persist in repeated interaction when defection dominates the one-shot Prisoner’s Dilemma?

**Status:** OPEN  
**Planned stage:** 0B

This should be answered using repeated-game incentives, strategy dependence, and population dynamics rather than anthropomorphic explanations.

---

## Q-003 — What is the relationship between evolutionary algorithms and replicator dynamics?

**Status:** OPEN  
**Planned stage:** 0B

When does a finite stochastic population process approximate deterministic replicator dynamics, and what assumptions are required?

---

## Q-004 — How should strategic diversity be measured?

**Status:** OPEN  
**Planned stages:** 0B / 4

Parameter variance may not equal behavioural diversity. What metrics capture genuinely different strategies or niches?

---

## Q-005 — How should non-transitive populations be evaluated?

**Status:** OPEN  
**Planned stages:** 0B / 2 / 4

If A beats B, B beats C, and C beats A, when do scalar ratings such as Elo become misleading and what alternatives are useful?

---

## Q-006 — When does self-play converge, cycle, or forget?

**Status:** OPEN  
**Planned stage:** 2

How do opponent sampling, historical pools, game structure, and learning dynamics affect robustness?

---

## Q-007 — What exactly creates non-stationarity in MARL?

**Status:** OPEN  
**Planned stage:** 3

Formalise how changing policies of other agents alter the effective transition/reward process observed by one learner.

---

## Q-008 — How can we distinguish emergent communication from accidental correlation?

**Status:** OPEN  
**Planned stage:** 4

What information-theoretic or intervention-based measurements show that learned messages actually influence coordinated behaviour?

---

## Q-009 — What makes an evaluator trustworthy enough for automated discovery?

**Status:** OPEN  
**Planned stages:** 6 / 7

How do we detect reward hacking, benchmark overfitting, evaluator exploitation, and invalid-but-high-scoring candidates?

---

## Q-010 — When does an LLM improve a search process rather than merely add expensive correlated samples?

**Status:** OPEN  
**Planned stage:** 7

Compare LLM-guided proposal distributions with random, evolutionary, and learned non-LLM generators under the same objective evaluator.
