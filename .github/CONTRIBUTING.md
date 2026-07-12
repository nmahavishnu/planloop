# Contributing to planloop

Contributions are welcome.

## The constraints

planloop is a single skill in a single file. Every change is judged against that:

- **It stays one file.** No second skill, no `references/` tree, no scripts, no per-tool variants. If a change needs more than SKILL.md, it is a different project.
- **New rules must earn their place.** Prefer rules that govern the interface between you and the agent (asking, planning, verifying) over ones that patch model behavior; patches age as models change. If you add a patch, say why it will still matter.
- **No invented numbers or unverifiable claims.** The skill tells the model not to fabricate metrics; the skill and README hold themselves to the same bar.
- **Cuts are as welcome as additions.** A PR that removes a rule that is no longer needed is a good PR.

## Good contributions

- Fixing a step that misfires or reads ambiguously.
- Tightening wording, removing redundancy, correcting an error.
- Portability fixes for a specific agent tool.
- A real-world failure case, with the run that produced it.

## Before you open a PR

- Read [SKILL.md](skills/planloop/SKILL.md) end to end. It is short.
- Say what problem the change solves.
- Keep the diff focused on one thing.
- Fill out the PR checklist that loads with your pull request, rather than deleting it.

## License

By contributing, you agree your contribution is licensed under the [MIT License](https://github.com/nmahavishnu/planloop/blob/main/LICENSE).