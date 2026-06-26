# CLAUDE.md anatomy

## Contents
- Purpose
- Thin index vs thick hub
- Section-by-section
- The link-index pattern
- When to split out

## Purpose

`CLAUDE.md` is the agent's entry point, read every session. It orients the agent
and routes it to the right `agent_docs/` file. It is not the place for full
subsystem detail; keep it the cheap, always-loaded routing layer.

Open with one line that sets the contract, e.g.:

> Guidance for agents working in this repo. Read this first, then the relevant
> file in `agent_docs/`.

## Thin index vs thick hub

**Thin index** (~80 lines). Subsystems are cleanly separable, so the hub stays
a dispatcher:

```
What this is        one paragraph + a small diagram
Layout              path → role table
Common commands     dev / build / run / deploy shell blocks
Docs                link list into agent_docs/, one line each
Conventions         the handful of non-negotiable rules
```

**Thick hub** (a few hundred lines). One dense codebase where most edits touch
shared rules, so design notes and invariants live inline but deepest detail
still links out:

```
What this is + links to the heaviest agent_docs
Layout
Build & run
Design notes        high-level per-subsystem, linking out for depth
Gotchas             warnings that don't fit elsewhere
Invariants          load-bearing rules that must stay true
Conventions         code style + Documentation Style block
Tooling             git/gh constraints, what not to run
```

Both shapes link into `agent_docs/`. The difference is how much they inline
before linking.

## Section-by-section

- **What this is**: one paragraph. An ASCII diagram of data flow earns its
  space here; prose paragraphs of rationale belong in the README.
- **Layout**: a table mapping paths to roles. Lets the agent find code without
  a tour.
- **Commands**: the exact shell commands for dev, test, build, run, deploy.
  Copy-pasteable.
- **Conventions**: hard rules only: code style, what not to run, naming.
  Phrase as "always/never" so they read as non-negotiable, not as suggestions.
- **Invariants**: see [agent-docs.md](agent-docs.md). The properties that must
  hold; the things a careless edit silently breaks.
- **Gotchas**: short pointers to traps; the long version lives in
  `agent_docs/gotchas.md`.
- **Documentation Style**: meta-rules for how the agent writes docs and
  user-facing copy. See [writing-style.md](writing-style.md).

## The link-index pattern

List every `agent_docs/` file with a markdown link and a one-line purpose. Add a
"read before you touch X" hint where it matters:

```markdown
## Docs
- [agent_docs/architecture.md](agent_docs/architecture.md): components, data flow, the core model.
- [agent_docs/aggregator.md](agent_docs/aggregator.md): backend internals: providers, cache, deploy.
- [agent_docs/gotchas.md](agent_docs/gotchas.md): non-obvious traps. Skim before touching deploy or settings.
```

Also link inline from a design note or invariant straight to the file that
explains it:

```markdown
The import graph must stay acyclic, see [agent_docs/architecture.md](agent_docs/architecture.md) "Module System".
```

Keep links one level deep: every agent_docs file reachable directly from
CLAUDE.md, not only via another doc. The one place depth resets is a project
boundary: in a monorepo an umbrella hub links to each package's CLAUDE.md, and
that package hub is a fresh one-level-deep root. See
[multi-project.md](multi-project.md).

**Link, don't `@`-import.** Use plain markdown links (`[agent_docs/x.md](agent_docs/x.md)`)
for deep dives, not Claude Code's `@path` import syntax. `@`-imports load
**eagerly**: the imported file's full content is pulled into context at session
start, so it costs the same as inlining and saves nothing. That defeats the whole
point of a routing hub. A markdown link is just a pointer the agent follows only
when a task needs it — that on-demand read is what makes the hub the cheap,
always-loaded layer. Reserve `@`-imports for the rare file you genuinely want in
context every session (and even then, prefer keeping it short and inline). The
trap: someone "tidies up" the link index into `@agent_docs/...` imports thinking
it is equivalent, and silently turns the whole `agent_docs/` tree into always-on
context.

Note moved docs so an agent doesn't hunt for them: "`plan-x.md` was promoted to
`architecture-x.md` (implemented)." See [agent-docs.md](agent-docs.md) for the
plan-to-architecture lifecycle.

Past ~10-15 docs a flat list stops scaling. Group the index into tables by
audience or function (app developers / contributors / plans), and add a
quick-reference table and a reading-order path near the top. See
[scaling.md](scaling.md).

## When to split out

Move content from CLAUDE.md into a new `agent_docs/<topic>.md` when:

- the file approaches ~500 lines, or
- a section grows into full subsystem reference (parser tables, shader layouts,
  algorithm derivations), or
- detail is only relevant to one subsystem an agent rarely touches.

Leave a one-line link behind. The hub shrinks back to routing.
