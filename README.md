# structuring-agent-docs

An agent skill that teaches how to structure a software project's documentation
so AI coding agents can navigate it efficiently.

It packages a repeatable model: a human-facing `README.md`, an agent-facing
`CLAUDE.md` hub, and a set of topic-focused `agent_docs/` deep dives that load
only when a task needs them. Progressive disclosure applied to a whole repo.

## What's here

```
SKILL.md                       # the skill: model, workflow, core rules
references/
  structure.md                 # directory layout and what belongs where
  claude-md.md                 # CLAUDE.md anatomy and the link-index pattern
  agent-docs.md                # naming, doc types, gotchas, invariants, generated docs
  writing-style.md             # agent-facing prose rules
  scaling.md                   # large/published doc sets: AGENTS.md alias, categorized index, llms.txt
  multi-project.md             # monorepos, sibling repos, multi-surface products
templates/
  CLAUDE.md                    # starter agent hub
  agent_docs/architecture.md   # subsystem deep-dive skeleton
  agent_docs/gotchas.md        # traps-and-findings skeleton
  agent_docs/plan.md           # saved-planning skeleton (promoted to architecture when built)
  umbrella/                    # standalone-umbrella hub + integration-note templates
MAINTAINING.md                 # trigger examples, duplicated-rule change-set
LICENSE                        # MIT
```

## Install

Copy or symlink the skill into a Claude Code skills directory:

```bash
# project-scoped
ln -s "$PWD" /path/to/project/.claude/skills/structuring-agent-docs
# or user-scoped
ln -s "$PWD" ~/.claude/skills/structuring-agent-docs
```

The agent loads it automatically when you ask how to set up or reorganize
`CLAUDE.md`, `AGENTS.md`, or `agent_docs/` for a repository.

## The model in one table

| Tier       | File(s)           | Audience | Role                                                               |
|------------|-------------------|----------|--------------------------------------------------------------------|
| User guide | `README.md`       | Humans   | What, why, quick start, how to use.                                |
| Agent hub  | `CLAUDE.md`       | Agents   | Read first: layout, commands, conventions, invariants, link index. |
| Deep dives | `agent_docs/*.md` | Agents   | One topic per file, loaded on demand.                              |

See [MAINTAINING.md](MAINTAINING.md) for trigger examples and the
duplicated-rule change-set.

## License

MIT — see [LICENSE](LICENSE).
