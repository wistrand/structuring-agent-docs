# Scaling docs as a project grows

The layered model holds at any size, but past a handful of `agent_docs/` files
and once docs get published, these methods keep the hub usable. Adopt them when
the pain appears; don't front-load them on a small project.

## Contents
- AGENTS.md and multi-tool naming
- Audience-categorized index
- Quick reference and reading order
- Role-split rules
- Project-local skills
- Philosophy tier
- Published docs and llms.txt
- Protecting generated copies

## AGENTS.md and multi-tool naming

Different agent tools look for different entry filenames. Keep one source of
truth and symlink the aliases:

```bash
ln -s CLAUDE.md AGENTS.md
```

One file, two names, zero drift. Add more aliases the same way if other tools
expect other names.

## Audience-categorized index

A flat link list stops scaling around 10-15 docs. Group the index into tables by
audience or function so a reader jumps to their lane instead of scanning
everything:

```markdown
## Documentation Index

### For app developers
| Topic           | Path                                                           |
|-----------------|----------------------------------------------------------------|
| Getting started | [agent_docs/getting-started.md](agent_docs/getting-started.md) |

### For contributors (internals)
| Topic           | Path                                                                   |
|-----------------|------------------------------------------------------------------------|
| Render pipeline | [agent_docs/architecture-render.md](agent_docs/architecture-render.md) |

### Plans (future work)
| Topic       | Path                                                             |
|-------------|------------------------------------------------------------------|
| Inline mode | [agent_docs/inline-mode-plan.md](agent_docs/inline-mode-plan.md) |
```

This separates current state from future work and user docs from internals, so
an agent isn't routed into a plan when it wanted current behavior.

## Quick reference and reading order

Two short sections near the top of a large CLAUDE.md:

- **Quick reference**: a `What | Where` table for the handful of things looked
  up constantly (entry point, run command, component catalog).
- **Reading order**: a numbered onboarding path for a fresh agent or new
  contributor: "1. getting-started → 2. tutorial → 3. common mistakes → …".
  It's a learning path, not a table of contents.

## Role-split rules

When a "Critical Rules" list grows long, the noise hurts. Split rules by who
needs them: framework-contributor rules stay in `CLAUDE.md`; app-author rules go
in the project-local skill's SKILL.md. Overlap the few that both need. Each
audience reads a shorter, relevant list instead of one 200-rule wall.

## Project-local skills

A repo can ship its own skill under `skills/<name>/SKILL.md` for a distinct
audience, e.g. people building *on* the project, not maintaining it. Keep it
self-contained: it mirrors the relevant slices of CLAUDE.md but is tuned to that
audience and omits internals. Link to it from CLAUDE.md's index.

When two or more projects share a domain, the shared conventions also belong in a
skill rather than copied into each CLAUDE.md, so siblings stay consistent from one
source. See [multi-project.md](multi-project.md).

## Philosophy tier

Separate "what and how" (the agent docs) from "why and when":

- **MANIFESTO.md**: the project's stance: problem, vision, what it is and isn't.
- **FAQ.md**: when to use and when *not* to; honest tradeoffs and limits.

These target decision-makers and skeptics, not an agent mid-edit. Keep them out
of the always-loaded hub; link from the README and the index.

## Published docs and llms.txt

If docs are published as a site, generate the site from the source markdown so
the two never diverge. A `build:docs` step typically:

1. copies `agent_docs/` and key root docs into the published tree,
2. generates an index page from CLAUDE.md's index tables,
3. emits an `llms.txt`: a flat list of doc titles and absolute URLs for LLM
   consumption.

Source stays markdown; HTML is build output, never hand-edited.

## Protecting generated copies

When a build copies docs into a published tree, an agent can mistake the copy
for the source and edit the wrong one. Add a hard rule in CLAUDE.md:

> Never edit `docs/reference/agent_docs/`; it's a generated copy. Edit
> `agent_docs/` and rebuild.
