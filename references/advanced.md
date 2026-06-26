# Beyond the base model

The README / CLAUDE.md / `agent_docs/` model holds at any size. Two kinds of growth
need a bit more, adopted only when the pain appears: one project that accumulates
docs or gets published, and work that spans several projects.

The alias that lets other agent tools find the hub (`ln -s CLAUDE.md AGENTS.md`) is
a base-model rule, not a scaling one; see SKILL.md "Core rules".

## When one project grows

A flat link index stops scaling around 10-15 docs. Past that:

- **Group the index by audience or function** so a reader jumps to their lane:
  app developers vs contributors, current state vs future plans. An agent then
  isn't routed into a plan doc when it wanted current behavior.
- **Add a quick-reference table** (`What | Where` for the few things looked up
  constantly: entry point, run command) and a **reading-order path** (a numbered
  onboarding sequence, not a table of contents) near the top of the hub.
- **Keep "why and when" out of the hub.** A MANIFESTO or FAQ for stance and
  tradeoffs targets decision-makers, not an agent mid-edit; link it from the
  README rather than loading it every session.
- **If docs are published as a site, generate it from the source markdown** so the
  two never diverge, and emit an `llms.txt` (a flat list of doc titles and URLs)
  for LLM consumption. Mark the generated copy so an agent edits the source, not
  the build output.

## When work spans projects

"Multi-project" is not one situation:

| Shape                      | What it is                         | What's shared         |
|----------------------------|------------------------------------|-----------------------|
| Monorepo                   | one repo, many packages, one build | layout, conventions   |
| Polyrepo / siblings        | related products, separate repos   | conventions, a domain |
| One product, many surfaces | client + server + device           | a contract or format  |

Three rules cover all three shapes:

- **The model nests, and depth resets at each boundary.** An umbrella hub (shared
  conventions, a package map, repo-wide invariants) links to each project's
  CLAUDE.md; that hub is itself a fresh one-level-deep root over its own
  `agent_docs/`. Nesting umbrella → project hub → deep dive is correct, not a
  violation of "one level deep".
- **Each child hub inherits, then documents only deltas.** A package hub opens
  with "Inherits the root conventions; package-specific deltas below," so the
  shared rules live once instead of being copied (and drifting) into each project.
- **Shared facts get exactly one owner.** A wire format, a status scale, an auth
  flow lives in one doc, in the project that owns it; everyone else links to it and
  nobody restates it. It matters most for a contract binding a client and server
  that must agree, where two copies drift the moment one side changes. A short
  labeled pointer
  ("authoritative source is X") is fine; an unlabeled second copy is the drift.

When siblings live in separate repos with no common root, the umbrella is itself a
repo: a table mapping each repo to its source and its CLAUDE.md, plus the
integration view (who provides and consumes which contract) that no single repo's
docs cover. It references each repo's own CLAUDE.md as the summary; it must not
keep a second hand-written copy. Relative markdown links break across repos, so
refer to siblings by name, or by published URL if they ship an `llms.txt`.
