---
name: structuring-agent-docs
description: Structures a software project's documentation so AI coding agents navigate and update it efficiently. Covers the README.md / CLAUDE.md / agent_docs/ split, a CLAUDE.md entry point that links one level deep to topic-focused agent_docs files (progressive disclosure), naming conventions, gotchas/findings docs, critical invariants, generated docs, and agent-facing writing style. Use when setting up or reorganizing CLAUDE.md, AGENTS.md, or agent_docs/ for a repository, when a CLAUDE.md has grown too large, or when the user asks how to document a codebase for coding agents.
---

# Structuring agent docs

Lay out a repository's documentation so an agent can pull in the context a task
needs without reading the whole codebase or one giant file. The model below is a
starting point, not a mandate — take the parts that fit a given project and skip
the rest.

The examples are from software projects, but the model fits any project whose work
lives in text artifacts — a book's chapters, a contract's clauses, a dataset's
tables. Read "source" as "the authoritative artifact," whatever that is for you.

## What belongs in a doc

When you document a codebase, let code carry what code can: if a rule can be a
type or a test, or a doc can be generated from source, do that: a prose doc that
restates what a type or test already guarantees only drifts out of sync. The
residue none of those can hold is the point of these docs, not a leftover: the
*why* behind a constraint, an approach tried and rejected, a platform trap that
cost an hour to diagnose. Write it because these docs are the agent's memory for
its future self — it has none between sessions, and "the why is in the commit" is
true but unreachable to an agent editing one file.

## The layered model

Three tiers, split by audience and by how much an agent loads at once:

| Tier        | File(s)                             | Audience | Role                                                                                                    |
|-------------|-------------------------------------|----------|---------------------------------------------------------------------------------------------------------|
| User guide  | `README.md`                         | Humans   | What it is, why, quick start, how to use. Narrative.                                                    |
| Entry point | `CLAUDE.md` (+ `AGENTS.md` symlink) | Agents   | "Read first." Layout, build/run, conventions, invariants, gotchas, and a link index into `agent_docs/`. |
| Deep dives  | `agent_docs/*.md`                   | Agents   | One topic per file, self-contained, loaded only when that subsystem is touched.                         |

Optional fourth tier: `docs/` for generated assets (screenshots, icons) and
`notes/` for informal design journals an agent doesn't rely on.

Why split this way: the agent reads `CLAUDE.md` every session, so keep it the
small routing entry point; reference in `agent_docs/` isn't loaded until the agent
follows a link. That trims the always-on context — but only to the extent the
agent follows the right links, which is the tradeoff under "Factor out only
what's safe to miss". It's the progressive-disclosure idea behind a skill's
SKILL.md, scaled to a whole repo.

## Portable model, named bindings

The structure above — the audience split, progressive disclosure, one-level-deep
linking, single-owner facts, capture-the-why — is tool-independent. The specific
names below are this skill's Claude Code bindings of those concepts; on another
toolchain, remap them. The model is the durable part; the filenames and the
import-mechanic warning are what's exposed to tooling churn, so when a tool renames
the entry point or drops a mechanism, change a row here, not the model.

| Concept (durable)                | Claude Code binding          |
|----------------------------------|------------------------------|
| Entry point, read first          | `CLAUDE.md`                  |
| Entry point always in context    | the harness auto-loads it    |
| Cross-tool alias for it          | `AGENTS.md` (symlink to it)  |
| On-demand deep dives             | `agent_docs/*.md`            |
| Eager whole-file import to avoid | Claude Code `@path` imports  |
| How this skill itself ships      | `SKILL.md` + bundled files   |

`agent_docs/` is a plain directory name, tool-independent. One binding is a
behavior, not a filename: the "cheap entry point" economics assume auto-load, so the
"read every session" phrasing is Claude Code behavior, not a law — without it the
always-on guarantee weakens and you lean harder on linking. `CLAUDE.md`, the
auto-load assumption, and the `@`-import warning are the first to revisit if the
ecosystem shifts (e.g. toward `AGENTS.md` as the primary entry point).

## Two shapes of CLAUDE.md

A thin entry point (~80 lines, pure routing) suits cleanly separable subsystems; a
thick entry point (a few hundred lines, design notes and invariants inline) suits one
dense codebase where most edits touch shared rules. Both still link out for the
deepest dives. When a CLAUDE.md crosses ~500 lines or starts carrying full
subsystem detail, extract that detail into an `agent_docs/<topic>.md` and leave a
link behind. See [references/claude-md.md](references/claude-md.md).

## Setup workflow

Copy this checklist when creating or reorganizing a project's agent docs:

```
- [ ] 1. Confirm README.md is human-facing (no agent rules leak in)
- [ ] 2. Create/trim CLAUDE.md as the agent entry point (see references/claude-md.md)
- [ ] 3. Extract per-subsystem detail into agent_docs/<topic>.md (see references/agent-docs.md)
- [ ] 4. Link every agent_docs file from CLAUDE.md, one level deep, markdown links
- [ ] 5. Add a Gotchas/findings doc for non-obvious traps and decision history
- [ ] 6. Add an Invariants section listing the rules that must stay true
- [ ] 7. Add a Documentation Style block (see references/writing-style.md)
- [ ] 8. Generate any doc derivable from source (settings, CLI --help) via a build
        target instead of hand-writing — the step most often skipped, first to rot
- [ ] 9. Set the update ritual: docs change with the code they point at; "update
        the docs" sweeps after big changes (see references/agent-docs.md "Keeping docs current")
```

Start from the templates in `templates/` and delete what doesn't apply.

## Core rules

- **One level deep.** Every `agent_docs/` file links directly from `CLAUDE.md`.
  Don't chain doc → doc → doc; deeply nested docs get skimmed or missed.
  (Cross-links *between* agent_docs are fine as extras.)
- **Link deep dives; don't eager-import them or bury them in backticks.**
  Write `[agent_docs/x.md](agent_docs/x.md)` for every doc an agent should follow,
  so the set is a navigable graph read on demand. Backticks stay fine for source
  paths (`src/cli.ts`), not for docs you want followed. Don't reference a deep dive
  with a mechanism that loads the whole file up front — in Claude Code that's
  `@path` imports, which enter context at session start and save nothing,
  defeating the entry point. See
  [references/claude-md.md](references/claude-md.md).
- **Factor out only what's safe to miss.** A deep dive saves context only if the
  agent follows the link; skip it and the fact is silently gone. So split by blast
  radius: what would silently corrupt an edit (invariants, data-loss gotchas,
  read-before-you-touch warnings) stays inline; only safely-skippable reference
  moves to `agent_docs/`. When in doubt, keep it inline. See
  [references/claude-md.md](references/claude-md.md) "Split by blast radius".
- **One topic per file, self-contained.** An agent editing audio reads
  `architecture-audio.md` alone, without first reading five others.
- **Name by type with a prefix.** `architecture-*`, `design-*`, `plan-*`,
  `research-*`, plus `gotchas`/`findings`. Kebab-case, descriptive, no numbers.
  Use the bare type name (`architecture.md`) while there is one doc of that type;
  switch to prefixes (`architecture-audio.md`, `architecture-net.md`) once a
  second one appears. A prefix can name a subsystem (`architecture-audio`) or a
  cross-cutting concern (`architecture-auth`); both are valid axes.
- **Capture the why.** A gotchas/findings doc holds non-obvious traps and the
  reasoning behind constraints, the things source code can't tell an agent. This
  is the residue from "What belongs in a doc": if a trap could be a test, add the
  test; what stays in prose is what no test can hold.
- **Point into the source, and let code win on conflict.** A doc says where each
  piece lives — file path plus key symbol — so it indexes the source instead of
  restating it from memory, and the agent jumps straight to the code. When a doc
  and the code disagree, the code is authoritative for what the system does now,
  the doc for why: trust the code, fix the doc. See
  [references/agent-docs.md](references/agent-docs.md).
- **Flag critical invariants** explicitly so an agent knows what must stay
  true before it changes anything. Put global, cross-cutting rules in CLAUDE.md's
  `Invariants` section; keep an agent_docs `Invariants` section to subsystem-local
  rules only. Never state the same rule in both — the two copies drift. If a
  subsystem doc needs a global invariant for context, link to CLAUDE.md instead
  of restating it.
- **Generate, don't drift.** Any doc derivable from code (settings, CLI `--help`,
  an API schema) should be produced by a build target, not hand-maintained —
  hand-listing options in the README silently falls out of sync. Easiest step to
  skip, first to rot; wire it up while the project is small. The same hazard applies
  to constants and code pasted into prose — see
  [references/writing-style.md](references/writing-style.md).
- **One entry-point file, aliased for other tools.** Keep a single source and
  symlink the names different tools read, so there's no second copy to drift.
  `ln -s CLAUDE.md AGENTS.md` covers Claude Code and the cross-tool `AGENTS.md`
  standard at once. The trick works only for tools that read a plain-markdown root
  file; ones with structured rule formats (Cursor, Copilot) read their own files
  with their own activation rules — generate or maintain those separately, don't
  symlink. On Windows, symlink creation needs Developer Mode or admin rights;
  where that's a blocker, make the alias a one-line stub that imports the
  canonical file instead.

## Beyond the base model

The base model holds at any size. Two kinds of growth need more, adopted only when
the pain appears: a single project that accumulates docs or gets published (an
audience-categorized index, reading-order onboarding, a published site), and work
that spans several projects (a monorepo, sibling repos, or
one product split across surfaces — the model nests, and shared facts get exactly
one owner the others reference). Both are in
[references/advanced.md](references/advanced.md).

## References

- [references/claude-md.md](references/claude-md.md): the full repo layout and what belongs where, CLAUDE.md anatomy, thin vs thick entry point, the link-index pattern, and what to factor out.
- [references/agent-docs.md](references/agent-docs.md): naming prefixes, doc types, gotchas/findings, invariants, generated docs, and dated data artifacts/fixtures.
- [references/writing-style.md](references/writing-style.md): agent-facing prose: no AI-isms, markdown links, aligned tables, no time-sensitive content.
- [references/advanced.md](references/advanced.md): beyond the base model — one project that grows large or published (audience index, onboarding, a published site) and work that spans projects (monorepos, siblings, surfaces: the nesting model, single-owner shared facts, umbrella mapping).

## Templates

- [templates/CLAUDE.md](templates/CLAUDE.md): starter entry point.
- [templates/agent_docs/architecture.md](templates/agent_docs/architecture.md): subsystem deep-dive skeleton.
- [templates/agent_docs/gotchas.md](templates/agent_docs/gotchas.md): traps-and-findings skeleton.
- [templates/agent_docs/plan.md](templates/agent_docs/plan.md): saved-planning skeleton, promoted to architecture when built.
- [templates/umbrella/CLAUDE.md](templates/umbrella/CLAUDE.md): standalone-umbrella entry point (repo table, shared contracts, integration view).
- [templates/umbrella/agent_docs/integration-note.md](templates/umbrella/agent_docs/integration-note.md): optional per-repo integration note.
