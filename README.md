# structuring-agent-docs

An agent skill that teaches how to structure a software project's documentation
so AI coding agents can navigate it efficiently.

It packages a repeatable model: a human-facing `README.md`, an agent-facing
`CLAUDE.md` hub, and a set of topic-focused `agent_docs/` deep dives loaded on
demand. Progressive disclosure applied to a whole repo.

## What's here

```
SKILL.md                       # the skill: model, workflow, core rules
references/
  claude-md.md                 # repo layout, CLAUDE.md anatomy, link-index pattern
  agent-docs.md                # naming, doc types, gotchas, invariants, generated docs
  writing-style.md             # agent-facing prose rules
  advanced.md                  # beyond the base model: one project that grows + work across projects
templates/
  CLAUDE.md                    # starter agent hub
  agent_docs/architecture.md   # subsystem deep-dive skeleton
  agent_docs/gotchas.md        # traps-and-findings skeleton
  agent_docs/plan.md           # saved-planning skeleton (promoted to architecture when built)
  umbrella/                    # standalone-umbrella hub + integration-note templates
CLAUDE.md                      # agent hub for editing this skill (AGENTS.md symlinks here)
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

See [CLAUDE.md](CLAUDE.md) for how to work in this repo: the editing discipline,
trigger examples, and deliberate non-goals.

## License

MIT — see [LICENSE](LICENSE).
