# structuring-agent-docs

An agent skill that teaches how to structure a software project's documentation
so AI coding agents can navigate and update it efficiently.

It packages a repeatable model: a human-facing `README.md`, an agent-facing
`CLAUDE.md` entry point, and a set of topic-focused `agent_docs/` deep dives loaded on
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
  project-README.md            # human-facing single-project README
  CLAUDE.md                    # starter entry point
  agent_docs/architecture.md   # subsystem deep-dive skeleton
  agent_docs/gotchas.md        # traps-and-findings skeleton
  agent_docs/plan.md           # saved-planning skeleton (promoted to architecture when built)
  umbrella/                    # umbrella entry point, project-README, and integration-note templates
CLAUDE.md                      # entry point for editing this skill (AGENTS.md symlinks here)
LICENSE                        # MIT
```

The benchmark that measures the skill's claims lives in a sibling repo:
<https://github.com/wistrand/structuring-agent-docs-benchmark>.

## Install

Skill payload, all that should be installed:

- `SKILL.md`
- `references/`
- `templates/`

Repo-only tooling, never part of an install: `docs/`, `.github/`, `README.md`,
`CLAUDE.md`. (None of it is executable; the benchmark that is lives in a separate repo.)

Download the payload-only zip and unzip it into a Claude Code skills directory:

```bash
curl -L -o structuring-agent-docs.zip \
  https://wistrand.github.io/structuring-agent-docs/structuring-agent-docs.zip
# the zip wraps everything in a structuring-agent-docs/ directory
unzip structuring-agent-docs.zip -d ~/.claude/skills/
```

Or, from a clone, copy the payload in (also payload-only):

```bash
mkdir -p ~/.claude/skills/structuring-agent-docs
cp SKILL.md ~/.claude/skills/structuring-agent-docs/
cp -R references templates ~/.claude/skills/structuring-agent-docs/
```

Development install: to hack on the skill, symlink the repo root with
`ln -s "$PWD" ~/.claude/skills/structuring-agent-docs`. This exposes the repo's docs and
CI config (`docs/`, `.github/`) to the agent, none of it executable, so it is not
strictly payload-only but adds no code.

The agent loads it automatically when you ask how to set up or reorganize `CLAUDE.md`,
`AGENTS.md`, or `agent_docs/` for a repository.

## The model in one table

| Tier        | File(s)           | Audience | Role                                                               |
|-------------|-------------------|----------|--------------------------------------------------------------------|
| User guide  | `README.md`       | Humans   | What, why, quick start, how to use.                                |
| Entry point | `CLAUDE.md`       | Agents   | Read first: layout, commands, conventions, invariants, link index. |
| Deep dives  | `agent_docs/*.md` | Agents   | One topic per file, loaded on demand.                              |

See [CLAUDE.md](CLAUDE.md) for how to work in this repo: the editing discipline,
trigger examples, and deliberate non-goals.

## Evidence

The central claim (keep critical facts inline, because factored-out ones get silently
missed) is measured, not just asserted. A dependency-free harness in a sibling repo tests
it across several models; see the
[findings](https://github.com/wistrand/structuring-agent-docs-benchmark/blob/main/findings.md)
and the [benchmark repo](https://github.com/wistrand/structuring-agent-docs-benchmark) to
reproduce. It is separate tooling, not part of the shipped skill.

## License

MIT. See [LICENSE](LICENSE).
