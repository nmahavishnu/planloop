<p align="center" style="margin-bottom: 0; line-height: 0;">
<img src="https://github.com/nmahavishnu/planloop/blob/main/planloop.png" width="500"> 
</p>
<br>

## ask. plan. delegate. verify. ask again.

An agent skill that turns requests into verified work and gets better as you use it. Built on two talks: [Andrej Karpathy](https://www.youtube.com/watch?v=96jN2OCOfLs&t=957s) on agentic engineering (you own the spec, the taste, and the quality bar; the agent owns the details) and [Aravind Srinivas](https://www.youtube.com/watch?v=MD4W_e3dJPs&t=248s) on questioning (progress comes from asking better questions, not just collecting better answers). planloop makes both mechanical.

```
 ask ─► plan ─► delegate ─► verify ─► ask again
                              │  ▲
                              ▼  │
                           fail → fix
```

_verify_ loops on failure up to three times, then surfaces the blocker. _ask again_ reports what is still open, which seeds the next round if you start one. Before the first question, _Step 0_ sends trivial tasks straight to work; before the close, a final pass re-checks everything together.

Agents drift without a target they can check. planloop gives them one, then holds the work to it.

### What it fixes

Agents start without asking what you actually want, and they stop without checking what they built. planloop guards both ends:

- **A few sharp questions, once.** One batched round, hard capped. Everything inferable becomes an assumption you can veto. No back-and-forth.
- **The plan is the contract.** Answers compile into plan.md with testable acceptance criteria. You approve it once. From there the plan gates the work, not the conversation.
- **A loop, not a checklist.** Per slice: implement, verify, fix, re-verify. Commands before judgment. Three attempts, then the blocker surfaces. Criteria never get quietly weakened to pass.
- **A final pass over the whole.** Slices that each pass on their own can still break where they meet, so the last step re-checks every criterion and invariant together.
- **A close that learns.** Finished work reports what is still open, and can propose a learning, saved to your learnings file, not this skill, for you to approve or reject.

### Why not just use a skill pack?

Skill packs turned into frameworks: dozens of files, commands to learn, personas to configure. Most of that weight patches model quirks that come and go with each release. planloop bets on the part that holds steady: the interface between your intent and the agent's work. Ask, write it down, check against it, learn. One file, works anywhere markdown skills work.

### Built to protect the context window

planloop keeps context lean at five points:

1. **plan.md replaces conversation replay.** A restarted or crashed session resumes from two small files, not a transcript.
2. **Sub-agents get a slice, never the history.** Each one reads its piece of the plan and returns a summary, so the main thread stays clean even when total work grows.
3. **One question round, not an interview.** Batched questions with proposed defaults cost one round trip.
4. **Capped retries kill the spiral.** Three attempts per slice, stop on repeated failure.
5. **Commands verify before judgment.** Tests and linters are nearly free. LLM self-review is not, so it runs only where commands cannot reach.

And tasks small enough that a plan would cost more than it saves never enter the loop: Step 0 routes them straight to work, before a single question is asked.

### You'll notice it working

- The agent asks a short batch of questions, or states its assumptions for you to veto, before writing anything.
- A plan.md appears and waits for your approval; the work follows it instead of the chat history.
- Verification runs against the criteria you approved, and blockers get surfaced instead of papered over.
- The run closes by telling you what is still open, and sometimes proposes a learning for you to keep or reject.

If work starts before any of this, the skill did not trigger. Say "use planloop" to force it.

### Install

**Claude Code, one command:**

```
/plugin marketplace add nmahavishnu/planloop
/plugin install planloop@planloop
```

**Or copy the file** into your agent's skills directory:

| Tool | Destination |
|---|---|
| Claude Code (project) | `.claude/skills/planloop/SKILL.md` |
| Claude Code (global) | `~/.claude/skills/planloop/SKILL.md` |
| Any tool with a skills directory | its skills folder, same layout |
| No skills system at all | paste the file into your agent instructions (AGENTS.md, rules file) |

### Use

1. **Automatic.** Installed, it triggers on non-trivial work and skips quick fixes. A hard single-file change counts; three trivial edits do not.
2. **By name.** Say "use planloop" to run it on anything, including tasks the trigger would skip.

### The skill learns

When a run exposes repeatable friction in the method, the agent proposes a short learning and you approve or reject it. Approved learnings live in `planloop.learnings.md` in your agent's user config directory (`~/.claude/` for Claude Code, `~/.codex/` for Codex, and so on), a separate file outside the skill, so updating or reinstalling planloop never erases what your copy has learned. The exact location is tool-specific, and the feature works best on tools with native skill discovery. Keep the file to about 20 lines; the method is identical for everyone, the learnings are yours.

Anything specific to one project stays in that project's memory, not the learnings file, which keeps the file about the method rather than any single codebase.

### License

Distributed under the MIT License. See [LICENSE](https://github.com/nmahavishnu/planloop/blob/main/LICENSE) for more information.

