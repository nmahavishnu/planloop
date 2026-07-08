---
name: planloop
description: 'A five-step loop for non-trivial work: ask a small batched round of questions, compile the answers into plan.md, delegate slices, verify against the plan''s own criteria, then close by surfacing what is still open. Use it when a task touches three or more files, spans more than one session, has any unresolved requirement, or when the user says "planloop". Skip it for single-file fixes, quick questions, and trivial edits.'
---

# planloop

Ask. Plan. Delegate. Verify. Ask again. State lives on disk, not in conversation.

At invocation, read your learnings file if it exists: `planloop.learnings.md` in your agent's user config directory (`~/.claude/` for Claude Code, `~/.codex/` for Codex, `~/.gemini/` for Gemini CLI, and so on). Its lines extend these rules.

## Step 0: Size the task

Run the loop if any gate is hit: the task touches 3 or more files, will span more than one session, has any unresolved requirement, or the user invoked planloop by name. No gate hit: skip to work with one confirming sentence, no questions, no plan file. The gates are the decision. Do not re-judge them.

## Step 1: Ask

One batched interview round up front. Deferred questions surface individually at their fork later; that is not a second interview.

Rules, in order:

1. **Infer first.** Mine the request, the codebase, existing conventions, and prior conversation before asking. Anything inferable becomes a stated assumption, not a question, unless a wrong guess would be costly or hard to reverse, in which case it is a question no matter how inferable.
2. **Decision relevance test.** A question earns a slot only if the answer would change plan.md. If every answer leads to the same plan, do not ask.
3. **Assumptions as vetoes.** Output format:
   - "Assumptions I am proceeding with (veto any): A, B, C."
   - "Questions I cannot infer: 1, 2."
   Silence on an assumption is consent.
4. **Proportional cap.** One gate hit: max 2 questions. Two or more gates hit, or any unresolved requirement: max 5. Never more.
5. **One line answers.** Every question is closed form with a proposed default: "A or B? I would pick A because X."
6. **Security hook (conditional).** If the task touches auth, user data, external input, or tool/MCP integrations, add one security question to the round. Otherwise add none.

Anything still unresolved goes into plan.md under Open Questions.

## Step 2: Plan

Compile answers into `plan.md`. Present it and stop. Do not start Step 3 until the user approves. The user approves the plan, not each step. From this point plan.md is the source of truth and conversation history stops mattering.

Template. Headings are the schema; the prose under them instructs you and is never copied into the plan:

```markdown
# Plan: <task name>

## Goal
One paragraph. What exists when this is done.

## Non-goals
What we are explicitly not building.

## Assumptions
The vetoed-or-accepted list from Step 1.

## Invariants
Things that must always hold, stated before work starts: cross-cutting rules in code (one persistent user ID ties all records; emails never correlate across systems), scope boundaries and required sources in research.

## Acceptance criteria
Every criterion is derived from the goal and the Step 1 answers. Never invent one the user did not state or imply; a made-up number is a fake contract. State how each will be checked: by command where one can run (test, linter, build), by an adversarial judgment pass where none can but the result can still be argued right or wrong (design, copy, research), or (checked by: human) where neither applies. Use only checks available in this environment, and decide the method now, at plan time.
Example of the form:
- [ ] <a behavior the task requires> (checked by: test / linter / build)
- [ ] <something no command can check> (checked by: human)   <- also how a hook-fired security criterion looks

## Task slices
Small and independently verifiable; prefer several small slices over a few large ones.
1. Slice name: what it delivers, which criteria it satisfies.
2. ...

## Open questions
Deferred questions, each tagged with the slice that will surface it.
```

Also create `progress.md`: an append-only log. One line per event: `YYYY-MM-DD slice | done/blocked | evidence or blocker`.

A crashed or restarted session resumes by reading these two files. Nothing else.

## Step 3: Delegate

Work slice by slice. By default, do the slices yourself in sequence. If sub-agents are available and some slices are independent, run those in parallel; the model decides what is safe to parallelize.

- A sub-agent receives only its slice of plan.md, never the conversation history.
- A sub-agent returns a short summary; only the parent appends to progress.md, so parallel work never races on the file.
- Read only your slice plus the files you are touching. Trust progress.md summaries for everything else. Do not re-explore the codebase per slice.

## Step 4: Verify

Per slice, this is a loop, not a checkpoint:

```
implement -> verify against the slice's criteria -> pass: log and next slice
                                                 -> fail: diagnose, fix, re-verify
```

A slice also re-checks any invariant its changes could touch, so cross-cutting breakage surfaces at the slice that caused it, not only in the final pass.

Rules:

1. **Deterministic first.** Verify with commands: tests, linter, build. If the check does not exist yet, writing it is part of the slice. A script counts as a check only if it is independent of the code it tests: a supplied fixture or spec, not one the agent writes to grade its own output. Command evidence goes in progress.md.
2. **Adversarial judgment (conditional).** Where no command can check the criterion (design, copy, research): ask one pointed question. Does this answer what was asked, and where does it fail? Output a short verdict and reason; never re-output the work. Use a fresh evaluator if sub-agents exist; otherwise say the judgment is your own. What neither commands nor judgment can verify goes to the human. Never self-certify silently.
3. **Attempt cap.** Max 3 attempts per slice, then stop and surface the blocker. Never weaken a criterion to pass it.
4. **No-progress rule.** Same failure twice in a row: stop. Do not retry the identical approach.
5. **Restart over patch.** If a slice is a mess, delete it and re-run from plan.md. Clean re-runs beat archaeology.
6. **Diff visibility.** Each slice logs a short summary of what changed and why to progress.md. Architectural choices are never merged silently; the human reviews diffs at their discretion.
7. **Flag weak code.** Tests passing is not proof the code is good. If you notice obvious duplication, dead weight, or a shaky abstraction, note it in progress.md for the human reviewing diffs. Do not pass it silently, and do not loop restarting on taste alone: judging your own taste is unreliable, so surfacing it is the job and deciding is the human's.
8. **Final pass.** After the last slice, re-verify the full acceptance criteria list and the invariants end to end. Slices passing individually does not mean the seams hold.

## Step 5: Ask again

Close the loop. Two moves, each conditional:

1. **Surface what is open.** Risks found, assumptions worth testing, the natural next step. Only what passes the decision-relevance test from Step 1. Zero is a valid answer, stated, not silently skipped.
2. **Propose a learning.** If the run exposed friction in the method itself, seen more than once, propose a short learning for the user to approve. Method-level only: repo quirks, commands, and conventions are project facts, not learnings, so they go to project memory. On approval, append it to your learnings file (see the top of this skill for its location); it takes effect next session. This SKILL.md itself never changes.

Most runs close with one sentence: nothing to surface, no learning to propose.

## Do not skip steps

| Excuse | Answer |
|---|---|
| "The task is simple, skip the questions." | Simple is not a feeling. Re-read the Step 0 gates; if any is hit, run the loop. |
| "The plan is obvious, skip plan.md." | Obvious plans are the cheapest to write down and the costliest to hold in context. Write it. |
| "It's close enough, mark it verified." | Close enough is not checked. Run its check, or flag it. A command criterion passes or fails; a judged one gets a verdict with a reason. |
| "There are no open questions worth surfacing." | Maybe. Apply the decision-relevance test and say so explicitly. Stating zero is closing the loop. Silence is skipping it. |
| "There is no attack surface here." | Check the four triggers first: auth, user data, external input, tool integrations. If none apply, the hook stays silent. If one applies, one question. |

## Tailored learnings

Approved learnings are stored in `planloop.learnings.md` in your agent's user config directory (`~/.claude/` for Claude Code, `~/.codex/` for Codex, `~/.gemini/` for Gemini CLI, and so on), created on the first approved learning. Storing it outside this skill means updating or reinstalling planloop never erases it. Keep the file to about 20 lines. This SKILL.md is the shared core and never mutates.