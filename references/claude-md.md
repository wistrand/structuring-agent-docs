# CLAUDE.md and repo layout

## Contents
- The full layout
- README, docs/, notes/: the other tiers
- Purpose of CLAUDE.md
- Thin vs thick entry point
- Section-by-section
- The link-index pattern
- When to split out
- Where does a fact go?

## The full layout

```
project/
├── README.md              # humans: what, why, quick start, how to use
├── CLAUDE.md              # agents: entry point + link index (or AGENTS.md)
├── agent_docs/            # agents: one topic per file, loaded on demand
│   ├── architecture.md        # or architecture-<subsystem>.md when large
│   ├── gotchas.md             # traps; or findings.md for diagnosed bugs
│   ├── design-<topic>.md      # spec + rationale for a format/feature
│   ├── plan-<topic>.md        # future work, not yet implemented
│   ├── research-<topic>.md    # investigations, citations, options weighed
│   └── settings.md            # GENERATED from source
├── docs/                  # generated assets: screenshots, icons, diagrams
└── notes/                 # optional: informal design journals, protocol dumps
```

Not every project needs every file. A small one may have only `README.md`,
`CLAUDE.md`, and two or three `agent_docs/` files. See
[agent-docs.md](agent-docs.md) for the naming prefixes and doc types.

## README, docs/, notes/: the other tiers

- **README.md**: for GitHub visitors, end users, and new contributors. Narrative,
  explains the "why". Holds the feature list, screenshots, quick start, how to use,
  supported platforms, the high-level architecture in prose, human build commands,
  license. Does *not* hold agent rules, critical invariants, code-style enforcement,
  or tooling constraints; those leak the wrong audience's concerns into the wrong
  file. It may link to `agent_docs/` for readers who want depth, but it is not the
  agent's entry point.
- **docs/**: binary/generated assets (screenshots, icons, diagrams), usually from a
  build target. Referenced from the README and `agent_docs/`, not hand-edited.
- **notes/**: informal, exploratory writing (design history, "five ways we could
  do X", raw protocol dumps). Not indexed in `CLAUDE.md` because an agent doesn't
  rely on it; link to it from the relevant `agent_docs/` file when it helps.

## Purpose of CLAUDE.md

`CLAUDE.md` is the agent's entry point, read every session. It orients the agent
and routes it to the right `agent_docs/` file. It is not the place for full
subsystem detail; keep it the cheap, always-loaded routing entry point.

Open with one line that sets the contract, e.g.:

> Guidance for agents working in this repo. Read this first, then the relevant
> file in `agent_docs/`.

## Thin vs thick entry point

**Thin entry point** (~80 lines). Subsystems are cleanly separable, so it stays
pure routing:

```
What this is        one paragraph + a small diagram
Layout              path → role table
Common commands     dev / build / run / deploy shell blocks
Docs                link list into agent_docs/, one line each
Conventions         the handful of non-negotiable rules
```

**Thick entry point** (a few hundred lines). One dense codebase where most edits touch
shared rules, so design notes and invariants live inline but deepest detail
still links out:

```
What this is + links to the heaviest agent_docs
Layout
Build & run
Design notes        high-level per-subsystem, linking out for depth
Gotchas             warnings that don't fit elsewhere
Invariants          the rules that must stay true
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

An entry point is an index of references, and the form of each reference follows
what it points at:

- **A local doc**: a deep dive in `agent_docs/`. A markdown link, kept one level
  deep (SKILL.md "Core rules"), followed on demand to read. The common case, and
  the rest of this section.
- **A source artifact**: the file (or chapter, clause, dataset) that holds the
  real thing, as a backtick path ideally naming the key symbol (`getTier()` in
  `src/detect.ts`); the agent opens it directly, not a doc to follow. See
  [agent-docs.md](agent-docs.md) "Point into the source".
- **Another repo's entry point**: across a project boundary, addressed by logical
  name or URL, since relative paths don't resolve across repos. See
  [advanced.md](advanced.md).

Same index pattern, three target types; what differs is only how the target is
addressed. For the local-doc case:

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

Keep links one level deep (SKILL.md "Core rules"). The link index is where that
rule binds: every agent_docs file reachable directly from CLAUDE.md, not only via
another doc. The one place depth resets is a project boundary: in a monorepo an
umbrella entry point links to each package's CLAUDE.md, and that package entry
point is a fresh one-level-deep root. See [advanced.md](advanced.md).

**Link, don't `@`-import.** Use plain markdown links (`[agent_docs/x.md](agent_docs/x.md)`)
for deep dives, not Claude Code's `@path` imports. `@`-imports load **eagerly**: the
file's full content enters context at session start, costing the same as inlining and
saving nothing, which defeats a routing entry point. A markdown link is a pointer the
agent follows only when a task needs it. Reserve `@`-imports for the rare file you
want in context every session (and keep it short). The trap: someone "tidies up" the
link index into `@agent_docs/...` imports thinking it's equivalent, silently turning
the whole `agent_docs/` tree into always-on context.

Note moved docs so an agent doesn't hunt for them: "`plan-x.md` was promoted to
`architecture-x.md` (implemented)." See [agent-docs.md](agent-docs.md) for the
plan-to-architecture lifecycle.

Past ~10-15 docs a flat list stops scaling. Group the index into tables by
audience or function (app developers / contributors / plans), and add a
quick-reference table and a reading-order path near the top. See
[advanced.md](advanced.md).

## When to split out

Move content from CLAUDE.md into a new `agent_docs/<topic>.md` when:

- the file approaches ~500 lines, or
- a section grows into full subsystem reference (parser tables, shader layouts,
  algorithm derivations), or
- detail is only relevant to one subsystem an agent rarely touches.

Leave a one-line link behind. The entry point shrinks back to routing.

### Split by blast radius, not just size

Size and topic say what *can* move; blast radius says what *should*. Splitting is
not free, and its cost is not the tokens but retrieval risk. The agent may not
follow the link, and that failure is **silent and asymmetric**:

- Keep a fact in the entry point and it is never relevant: you wasted some tokens, but the
  agent had the fact.
- Move a fact to a deep dive and the agent skips the link: the agent now operates
  *without* the fact and has no signal that it is missing. The edit proceeds on
  incomplete context and looks fine until it isn't.

The entry point is the one tier exempt from this risk, because Claude Code
auto-loads it every session, so it is in context whether or not the agent chooses to
read a link. (That auto-load is a binding, not a law; on a tool without it the
exemption weakens, see SKILL.md "Portable model, named bindings".) So the rule is: **anything whose absence silently corrupts an edit stays in
the entry point.** Critical invariants, data-loss gotchas, and read-before-you-touch
warnings are kept inline (and *also* linked from the relevant deep dive as
context), never relocated wholesale into a file the agent has to remember to open.
What moves to `agent_docs/` is reference an agent pulls in deliberately when it
works on that subsystem: material it can safely not load on an unrelated task.

This bounds the token-savings argument honestly: factoring out optimizes the
best case (perfect retrieval, minimal context) and pays for it with a worst case
(silent information loss) that a single fat file does not have. Spend that trade
only where the worst case is "the agent re-reads code it could have been told
about," not "the agent breaks an invariant it never saw."

## Where does a fact go?

- A user needs it to run or use the app → `README.md`
- The agent needs it every session (a command, a hard rule, a layout) → `CLAUDE.md`
- It's deep detail about one subsystem → `agent_docs/architecture-<sub>.md`
- It's a non-obvious trap or "why is it like this" → `agent_docs/gotchas.md`
- It's not built yet → `agent_docs/plan-<topic>.md`
- It's derivable from source → generate it, don't write it
- It's background colour, not needed to make changes → `notes/`
- It's shared across projects → the one owning entry point, referenced from the others
  (see [advanced.md](advanced.md))
