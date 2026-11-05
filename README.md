# atomic-commits

A [Claude Code](https://claude.com/claude-code) plugin that commits finished work as small, atomic commits.

When a unit of work is done and its checks pass, Claude invokes the `atomic-commits` skill. The skill runs in a forked context on a dedicated `committer` agent (Sonnet, Bash only), so the commit work stays out of your main conversation. It:

- splits changes into one commit per idea you can describe without "and"
- puts mechanical changes (renames, moves, formatting) first, in their own commits
- keeps tests with the code they test, and lockfiles with their dependency change
- stages parts of files with patches when one file holds several changes
- runs the project's check and tests before committing, and refuses to commit if they fail
- writes plain imperative subjects of 50 characters or fewer, with no `feat:` prefixes
- folds late fixes into earlier unpushed commits with `--fixup` and autosquash, and never rewrites pushed history

## Install

In Claude Code:

```
/plugin marketplace add jonnyvango/atomic-commits
/plugin install atomic-commits@atomic-commits
```

## Use

Claude picks the skill up by itself when work is ready to commit. You can also run it directly with `/atomic-commits`.

To make Claude commit as it goes, add this to your `~/.claude/CLAUDE.md`:

```markdown
## Commits

Commit as you go, following the `atomic-commits` skill: small, focused commits made while working, not one large commit at the end.
```

## Layout

```
.claude-plugin/plugin.json        plugin manifest
.claude-plugin/marketplace.json   lets the repo act as its own marketplace
skills/atomic-commits/SKILL.md    the skill
agents/committer.md               the agent the skill runs on
```

## License

[MIT](LICENSE)
