# Contributing to planloop

Contributions are welcome.

## The one constraint

planloop is a single skill in a single file. Before opening a PR, check your change against it:

- **It stays one file.** No second skill, no `references/` tree, no scripts, no per-tool variants. If a change needs more than SKILL.md, it is a different project.
- **New rules must earn their place.** Every rule in the skill costs tokens on each run and attention from the reader. Some rules patch a passing model quirk and stop earning their place as models change; others govern the interface between the user and the agent (asking, planning, verifying) and stay useful regardless. Prefer the second kind, and if a rule patches model behavior, say why it will still matter.
- **No invented numbers or unverifiable claims.** The skill tells the model not to fabricate metrics; the skill and README hold themselves to the same bar.
- **Cuts are as welcome as additions.** A PR that removes a rule that is no longer needed is a good PR.

## Good contributions

- Fixing a step that misfires or reads ambiguously.
- Tightening wording, removing redundancy, correcting an error.
- Portability fixes for a specific agent tool, as long as they stay in the one file.
- A real-world failure case, with the run that produced it.

## Before you open a PR

- Read [SKILL.md](skills/planloop/SKILL.md) end to end. It is short.
- Say what problem the change solves and, for any added rule, why it will not go stale over time.
- Keep the diff focused on one thing.

## License

By contributing, you agree your contribution is licensed under the MIT License.