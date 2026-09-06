# SESSION_CLOSEOUT.md

Use this checklist after any session that materially changes project knowledge, decisions, implementation, or experimental state.

The goal is not administrative completeness. The goal is that a future chat can continue **without the original conversation**.

## 1. Ask: what changed?

Identify only durable changes:

- project purpose or constraints;
- conceptual understanding;
- accepted/rejected decisions;
- experiment design/state;
- implementation state;
- important failures or discoveries;
- unresolved questions;
- next step.

Do not save conversational filler.

## 2. Update CURRENT_STATE.md

`CURRENT_STATE.md` should always answer:

- Where are we?
- What was just completed?
- What is currently active?
- What is explicitly not decided or not implemented?
- What should happen next?
- Which files must the next session read?

Keep it concise enough to serve as a handoff document.

## 3. Update decisions if necessary

If a durable technical, scientific, or pedagogical choice was made:

- append a `DEC-XXX` record;
- include reason and alternatives where relevant;
- never silently rewrite the rationale of an earlier accepted decision;
- mark superseded decisions explicitly.

## 4. Update the active experiment

If an experiment changed:

- status;
- hypothesis/design;
- implementation reference;
- configuration;
- results;
- observations;
- interpretation;
- limitations.

Keep observations separate from interpretation.

## 5. Update conceptual knowledge

If a concept became clearer or a derivation was completed, update the relevant theory note, concept map, or glossary.

Do not write a polished note that implies understanding Jonathan has not actually reached.

## 6. Update OPEN_QUESTIONS.md

Add new durable questions.

Close or partially answer old questions only when there is an actual argument, derivation, experiment, or source supporting the update.

## 7. Update the learning journal when useful

Record a learning entry only if something meaningful changed in Jonathan’s understanding, recall, or misconceptions.

## 8. Update references

Add only sources that materially contributed to the current work.

## 9. Git checkpoint

Commit the coherent documentation/code change with a meaningful message.

For important learning-stage completions, consider a Git tag such as:

`milestone/stage-0-evolutionary-games`

Tags should mark actual completion gates, not arbitrary dates.

## 10. Restart test

Before considering the session closed, imagine a new assistant with access only to the repository.

Could it determine:

1. the stable mission?
2. the pedagogical rules?
3. the current stage?
4. what has actually been done?
5. what remains provisional?
6. the next action?
7. why that next action comes next?

If not, fix the documentation before relying on the chat to preserve the missing context.
