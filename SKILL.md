---
name: structuring-agent-docs
description: Structures a software project's documentation so AI coding agents navigate it efficiently. Covers the README.md / CLAUDE.md / agent_docs/ split, a CLAUDE.md hub that links one level deep to topic-focused agent_docs files (progressive disclosure), naming conventions, gotchas/findings docs, load-bearing invariants, generated docs, and agent-facing writing style. Use when setting up or reorganizing CLAUDE.md, AGENTS.md, or agent_docs/ for a repository, when a CLAUDE.md has grown too large, or when the user asks how to document a codebase for coding agents.
---

# Structuring agent docs

Lay out a repository's documentation so an agent picks up only the context a
task needs, instead of reading the whole codebase or one giant file. The model
below is distilled from real projects that an agent works in daily.

## The layered model

Three tiers, split by audience and by how much an agent loads at once:

| Tier       | File(s)                             | Audience | Role                                                                                                    |
|------------|-------------------------------------|----------|---------------------------------------------------------------------------------------------------------|
| User guide | `README.md`                         | Humans   | What it is, why, quick start, how to use. Narrative.                                                    |
| Agent hub  | `CLAUDE.md` (+ `AGENTS.md` symlink) | Agents   | "Read first." Layout, build/run, conventions, invariants, gotchas, and a link index into `agent_docs/`. |
| Deep dives | `agent_docs/*.md`                   | Agents   | One topic per file, self-contained, loaded only when that subsystem is touched.                         |

Optional fourth tier: `docs/` for generated assets (screenshots, icons) and
`notes/` for informal design journals that aren't load-bearing.

Why split this way: the agent reads `CLAUDE.md` every session, so it stays the
cheap routing layer. Heavy reference lives in `agent_docs/` and costs nothing
until the agent follows a link. This is the same progressive-disclosure idea as
a skill's SKILL.md pointing to bundled files, scaled to a whole repo.

## Two shapes of CLAUDE.md

Pick by project size:

- **Thin index** (~80 lines): one-paragraph "what this is", layout table,
  common commands, a handful of hard conventions, then a link list into
  `agent_docs/`. Best when subsystems are cleanly separable.
- **Thick hub** (a few hundred lines): the above plus design notes, a Gotchas
  section, and invariants inline, but still links out for the deepest dives.
  Best for a single dense codebase where most edits touch shared rules.

When a CLAUDE.md crosses ~500 lines or starts carrying full subsystem detail,
extract that detail into an `agent_docs/<topic>.md` and leave a link behind.

## Setup workflow

Copy this checklist when creating or reorganizing a project's agent docs:

```
- [ ] 1. Confirm README.md is human-facing (no agent rules leak in)
- [ ] 2. Create/trim CLAUDE.md as the agent entry point (see references/claude-md.md)
- [ ] 3. Extract per-subsystem detail into agent_docs/<topic>.md (see references/agent-docs.md)
- [ ] 4. Link every agent_docs file from CLAUDE.md, one level deep, markdown links
- [ ] 5. Add a Gotchas/findings doc for non-obvious traps and decision history
- [ ] 6. Add an Invariants section listing load-bearing rules
- [ ] 7. Add a Documentation Style block (see references/writing-style.md)
- [ ] 8. Generate any doc derivable from source (settings, CLI --help) via a build
        target instead of hand-writing — the step most often skipped, first to rot
```

Start from the templates in `templates/` and delete what doesn't apply.

## Core rules

- **One level deep.** Every `agent_docs/` file links directly from `CLAUDE.md`.
  Don't chain doc → doc → doc; an agent previews nested files with `head` and
  misses content. (Cross-links *between* agent_docs are fine as extras.)
- **Markdown links for navigable docs, not backticks, and never `@`-imports.**
  Write `[agent_docs/architecture.md](agent_docs/architecture.md)` for every doc
  an agent should be able to follow, so the doc set is a graph, not a pile of
  filenames. Backticks are fine for source paths in a Layout table or inline
  code (`src/cli.ts`) — the rule is about doc references you want followed, not
  every filename. Do not turn deep-dive links into Claude Code `@path` imports:
  those load eagerly at session start (the full file enters context, saving
  nothing), which defeats the routing hub. Markdown links are read on demand;
  that on-demand read is the whole point. See
  [references/claude-md.md](references/claude-md.md).
- **One topic per file, self-contained.** An agent editing audio reads
  `architecture-audio.md` alone, without first reading five others.
- **Name by type with a prefix.** `architecture-*`, `design-*`, `plan-*`,
  `research-*`, plus `gotchas`/`findings`. Kebab-case, descriptive, no numbers.
  Use the bare type name (`architecture.md`) while there is one doc of that type;
  switch to prefixes (`architecture-audio.md`, `architecture-net.md`) once a
  second one appears.
- **Capture the why.** A gotchas/findings doc holds non-obvious traps and the
  reasoning behind constraints, the things source code can't tell an agent.
- **Flag load-bearing invariants** explicitly so an agent knows what must stay
  true before it changes anything. Put global, cross-cutting rules in CLAUDE.md's
  `Invariants` section; keep an agent_docs `Invariants` section to subsystem-local
  rules only. Never state the same rule in both — the two copies drift. If a
  subsystem doc needs a global invariant for context, link to CLAUDE.md instead
  of restating it.
- **Generate, don't drift.** Any doc derivable from code (settings, CLI `--help`,
  an API schema) should be produced by a build target, not maintained by hand. If
  the CLI prints its own help, add a task that captures it into a doc
  (`deno task gen-docs > docs/cli.md`) rather than hand-listing options in the
  README, where they silently fall out of sync. This step is the easiest to skip
  and the first to rot — wire it up when the project is small. The same hazard
  applies to lone constants (sizes, caps, timeouts, versions) pasted into prose:
  name the constant and its home, don't copy the value (see
  [references/writing-style.md](references/writing-style.md)).
- **Alias for other tools.** Symlink `AGENTS.md → CLAUDE.md` so tools expecting
  a different entry filename find one source of truth, not a stale copy.

## Scaling up

A flat link index works to ~10-15 docs. Past that, or once docs are published,
the hub needs more structure: an audience-categorized index, a quick-reference
table, a reading-order onboarding path, role-split rules, a generated docs site
with `llms.txt`, and a philosophy tier (MANIFESTO/FAQ). Adopt these when the pain
appears, not before. See [references/scaling.md](references/scaling.md).

When work spans several projects (a monorepo, sibling repos, or one product split
across surfaces), the model nests: an umbrella hub above per-project hubs, with
shared facts owned by exactly one doc that the others reference. See
[references/multi-project.md](references/multi-project.md).

## References

- [references/structure.md](references/structure.md): full directory layout, the README vs CLAUDE.md vs agent_docs split, and what belongs where.
- [references/claude-md.md](references/claude-md.md): section-by-section CLAUDE.md anatomy, thin-index vs thick-hub, and the link-index pattern.
- [references/agent-docs.md](references/agent-docs.md): naming prefixes, doc types, gotchas/findings, invariants, generated docs, and dated data artifacts/fixtures.
- [references/writing-style.md](references/writing-style.md): agent-facing prose: no AI-isms, markdown links, aligned tables, no time-sensitive content.
- [references/scaling.md](references/scaling.md): patterns for large/published doc sets: AGENTS.md alias, audience-categorized index, reading order, role-split rules, project-local skills, philosophy tier, generated site + llms.txt.
- [references/multi-project.md](references/multi-project.md): docs across monorepos, sibling repos, and multi-surface products: the recursive model, depth reset, single-owner shared facts, the standalone umbrella (repo table + integration view) and cross-repo linking.

## Templates

- [templates/CLAUDE.md](templates/CLAUDE.md): starter agent hub.
- [templates/agent_docs/architecture.md](templates/agent_docs/architecture.md): subsystem deep-dive skeleton.
- [templates/agent_docs/gotchas.md](templates/agent_docs/gotchas.md): traps-and-findings skeleton.
- [templates/agent_docs/plan.md](templates/agent_docs/plan.md): saved-planning skeleton, promoted to architecture when built.
- [templates/umbrella/CLAUDE.md](templates/umbrella/CLAUDE.md): standalone-umbrella hub (repo table, shared contracts, integration view).
- [templates/umbrella/agent_docs/integration-note.md](templates/umbrella/agent_docs/integration-note.md): optional per-repo integration note.
