# Directory structure and what belongs where

## Contents
- The full layout
- README.md: the human tier
- CLAUDE.md: the agent hub
- agent_docs/: the deep dives
- docs/ and notes/: assets and journals
- Decision: where does a given fact go?

## The full layout

```
project/
├── README.md              # humans: what, why, quick start, how to use
├── CLAUDE.md              # agents: entry point + link index (or AGENTS.md)
├── agent_docs/            # agents: one topic per file, loaded on demand
│   ├── architecture.md        # or architecture-<subsystem>.md when large
│   ├── architecture-<sub>.md
│   ├── gotchas.md             # traps; or findings.md for diagnosed bugs
│   ├── design-<topic>.md      # spec + rationale for a format/feature
│   ├── plan-<topic>.md        # future work, not yet implemented
│   ├── research-<topic>.md    # investigations, citations, options weighed
│   ├── settings.md            # GENERATED from source
│   └── store-listing.md       # user-facing release copy
├── docs/                  # generated assets: screenshots, icons, hero images
└── notes/                 # optional: informal design journals, protocol dumps
```

Not every project needs every file. A small project may have only `README.md`,
`CLAUDE.md`, and two or three `agent_docs/` files.

## README.md: the human tier

Audience: GitHub visitors, end users, new contributors. Tone: narrative,
explains the "why".

Holds: feature list, screenshots, quick start, how to use / interact, supported
platforms or devices, the high-level algorithm or architecture in prose, build
commands for a human, license.

Does not hold: agent rules, load-bearing invariants, code-style enforcement,
tooling constraints, or "do not run tests unless…" meta-instructions. Those leak
the wrong audience's concerns into the wrong file.

A README's documentation section may link to `agent_docs/` for readers who want
depth, but the README is not the agent's entry point.

## CLAUDE.md: the agent hub

Audience: the coding agent, every session. Tone: prescriptive, dense, no filler.
Opens with a one-line "read this first" and a pointer to `agent_docs/`.

It is the routing layer: enough context to orient, then links out. See
[claude-md.md](claude-md.md) for its section anatomy. Keep it the cheap layer.
When it starts carrying full subsystem detail, extract that to `agent_docs/`.

If the project also targets other agent tools, don't duplicate the file;
symlink `AGENTS.md → CLAUDE.md` so there's one source of truth (see
[scaling.md](scaling.md)).

## agent_docs/: the deep dives

Audience: the agent, but only when working that subsystem. Each file is a
self-contained reference an agent can read alone. This is where the bulk of
real knowledge lives, kept out of the always-loaded hub.

See [agent-docs.md](agent-docs.md) for naming prefixes and the doc types.

## docs/ and notes/: assets and journals

- `docs/`: binary/generated assets such as screenshots, store icons, hero images.
  Often produced by a build target (`make gallery`). Referenced from
  `agent_docs/store-listing.md` and the README.
- `notes/`: informal, exploratory writing such as design history, "five ways we could
  do X", raw hardware protocol dumps. Not indexed in `CLAUDE.md` because it
  isn't load-bearing. Useful context, not instructions. Link to it from the
  relevant `agent_docs/` file when it helps.

## Decision: where does a given fact go?

- A user needs it to run or use the app → README.md
- The agent needs it every session (a command, a hard rule, a layout) → CLAUDE.md
- It's deep detail about one subsystem → agent_docs/architecture-<sub>.md
- It's a non-obvious trap or "why is it like this" → agent_docs/gotchas.md
- It's not built yet → agent_docs/plan-<topic>.md
- It's derivable from source → generate it, don't write it
- It's background colour, not needed to make changes → notes/
- It's shared across projects → the one owning hub, referenced from the others (see [multi-project.md](multi-project.md))
