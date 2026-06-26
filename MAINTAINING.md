# Maintaining this skill

Maintainer-facing notes. Not loaded by the agent at runtime — the skill itself
is `SKILL.md` plus `references/` and `templates/`.

## Commits

This project deliberately skips commit-message conventions. The history is the
log and the diffs, which show what changed and why, so a short subject line is
enough; no structured format, no body, no CHANGELOG. This is a chosen tradeoff,
not an oversight: don't "fix" it by backfilling prose messages or imposing a
convention.

## Cross-file links

The skill's main rot risk is cross-file markdown links going stale. When you
rename or move a file under `references/` or `templates/`, grep for inbound
links and fix them, and confirm every deep dive is still linked from `SKILL.md`:

```bash
grep -rn "(references/\|templates/" SKILL.md references
```

Distinguish navigable links (in prose) from illustrative ones (in fenced or
inline code) — only the prose links must resolve.

### Why no link-checker script

A script to automate this was considered and deliberately rejected. This skill
is pure documentation (prose, references, templates), and shipping an
executable validator would change what it is: an agent that loads it for advice
on structuring docs should not also be made to run code from it. The checking it
would do is also low-volume (a few dozen links) and easy to do by hand or with
the grep above, so the cost (a runtime dependency, a maintained script, the
surprise of code in a docs skill) isn't worth it. Keep this skill script-free.
If link rot ever becomes a real, recurring problem, revisit, but the bar is a
demonstrated need, not theoretical tidiness.

## No example project

Don't add a full worked example project (a fake repo with a filled README,
CLAUDE.md, and agent_docs/). It was considered and rejected. The templates
already supply worked structure, and a fake codebase is the most drift-prone
artifact possible: its docs must stay consistent both internally and with the
skill's rules as they change, which is the exact failure the skill warns
against. A single example also over-anchors, readers cargo-cult its specific
choices as rules, and a toy small enough to maintain can't demonstrate the
scaling and multi-project rules that most need it.

The concreteness it would add is better delivered by inline filled excerpts next
to the rule they illustrate (see the `findings.md` and `Invariants` examples in
[references/agent-docs.md](references/agent-docs.md), and the link-index block in
[references/claude-md.md](references/claude-md.md)). If a spot reads too
abstract, add another small excerpt there, not a project.

## Trigger examples

The `description` frontmatter in `SKILL.md` is the only thing deciding when the
skill auto-loads. These are the prompts it is tuned to catch — and the
near-misses it should leave alone. Re-check both lists after editing the
description.

Should fire:

- "Help me set up a CLAUDE.md for this repo." — core case.
- "My CLAUDE.md is 900 lines and unwieldy; how do I split it?" — the
  too-large-hub trigger.
- "How should I organize agent_docs / AGENTS.md so Claude navigates the repo?" —
  the agent-navigation framing.
- "We have a monorepo — how do we document it for coding agents?" — multi-project
  case, still in scope.

Should NOT fire (doc-adjacent but out of scope):

- "Write the README intro for my library." — human-facing prose, not agent
  navigation structure.
- "Add JSDoc/docstrings to these functions." — in-code API docs, a different job.
- "Draft a blog post about our architecture." — narrative for people.
- "Generate API reference docs from my OpenAPI spec." — doc generation tooling,
  not the README/CLAUDE.md/agent_docs split.

The split is the test: in scope when the ask is about *how an agent picks up
repo context*; out of scope when it's human-facing prose or code-level docs.

## Duplicated load-bearing rules

A few rules are deliberately restated across standalone reference files so each
reads alone when followed in isolation — this is reinforcement, not the
code-derived drift the skill warns against. But if the underlying model changes,
every copy must change together. This is the change-set, not a mandate to
de-duplicate. Owner = the statement to edit first; satellites should stay
consistent with it.

| Rule                       | Owner (edit first)         | Satellites to keep in sync                                        |
|----------------------------|----------------------------|------------------------------------------------------------------|
| One level deep             | `SKILL.md` "Core rules"    | `references/claude-md.md`, `references/writing-style.md`, `references/multi-project.md` (boundary-reset variant) |
| `AGENTS.md → CLAUDE.md` alias | `SKILL.md` "Core rules" | `references/structure.md`, `references/scaling.md`               |
| Generate, don't drift      | `SKILL.md` "Core rules"    | `references/agent-docs.md`, `references/structure.md`            |
| Single-owner shared facts  | `references/multi-project.md` | `SKILL.md` "Scaling up" (one-line summary reference)          |

If a fifth duplicate appears, either give it an owner here or collapse it to a
link — don't let it spread untracked.
