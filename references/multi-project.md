# Documentation across multiple projects

The base model assumes one repo with one hub. When work spans several projects,
the model still applies, but it has to nest and the shared facts need owners.

## Contents
- The three shapes
- The recursive model
- Depth resets at each boundary
- Inheritance and delta
- Single source of truth for shared facts
- Shared conventions become a skill
- Global vs project scope
- Standalone umbrella
- Cross-repo linking
- Decision guide

## The three shapes

"Multi-project" is not one situation. The handling differs by shape:

| Shape                      | What it is                            | What's shared         |
|----------------------------|---------------------------------------|-----------------------|
| Monorepo                   | one repo, many packages, one build    | layout, conventions   |
| Polyrepo / siblings        | related products in separate repos    | conventions, a domain |
| One product, many surfaces | client + server + device, split apart | a contract or format  |

Name the shape first; it decides which mechanisms below apply.

## The recursive model

The README / CLAUDE.md / `agent_docs/` hub-and-deep-dives pattern nests. Apply it
at each level:

```
repo-root/
├── CLAUDE.md            # umbrella hub: shared conventions, package map, repo-wide invariants
├── agent_docs/          # cross-cutting deep dives (the contract, the shared pipeline)
└── packages/
    ├── web/
    │   ├── CLAUDE.md     # package hub: web build/run/gotchas; inherits root conventions
    │   └── agent_docs/
    └── watch/
        ├── CLAUDE.md
        └── agent_docs/
```

The root CLAUDE.md is an umbrella hub: shared conventions, repo-wide invariants, a
package map, and build orchestration. Each package is the full base model again.

## Depth resets at each boundary

The "one level deep" rule restarts at every project boundary. From the root you
reach a package hub (one hop); from that hub you reach its deep dives (one hop
again). Each hub is a fresh entry point, so the depth counter resets. Nesting
root hub to package hub to package deep-dive is correct, not a violation, because
the package hub is itself a one-level-deep root.

## Inheritance and delta

"Don't restate what an agent already knows" extends to "don't restate what a
parent hub already states." A package hub opens with a line like:

> Inherits the root conventions. Package-specific deltas below.

Then it documents only what differs. This is the main defense against the drift
that polyrepos and monorepos suffer when each project copies the shared rules.

## Single source of truth for shared facts

A fact shared across projects (a wire format, a status scale, an auth flow) gets
exactly one owner: one doc, in the project that owns the fact. Everyone else
links to it. Nobody re-states it.

This is the highest-value rule for the many-surfaces shape, where a contract
binds a client and a server that must agree. State it as an invariant in the
owning project, and have the other surfaces reference it rather than describe
their own copy, which would drift the moment one side changes.

The owner doesn't have to be one of the siblings. When a fact is shared across
siblings and no single repo is its natural home (an inter-service wire format, a
status scale), the umbrella project can own it, and the siblings reference up.
See "Standalone umbrella" below. A short, clearly-labeled summary of a fact owned
elsewhere ("authoritative source is X") is not a restatement; it is a sourced
pointer. An unlabeled second copy is.

## Shared conventions become a skill

When two or more projects share a domain, the shared conventions belong in a
skill, not copied into each CLAUDE.md. A skill loads on demand and lives once, so
it is the cleanest way to keep siblings consistent. Each project's CLAUDE.md then
says "follow the <domain> skill" and documents only its own specifics. See
[scaling.md](scaling.md) for project-local skills.

## Global vs project scope

There are three tiers of always-loaded guidance, and each owns different facts:

| Tier                  | Owns                                           |
|-----------------------|------------------------------------------------|
| `~/.claude/CLAUDE.md` | the author's universal preferences             |
| Repo umbrella hub     | repo-wide conventions, the package map         |
| Package / repo hub    | that project's build, run, gotchas, invariants |

Keep them separate. Personal preferences don't belong in a repo hub, and project
facts don't belong in the global file.

## Standalone umbrella

Siblings in separate repos have no common root, so the umbrella is itself a repo:
a top-level map of the set of repos, how they relate, and where each shared
contract lives. It has to be useful on its own, for an agent that has only the
umbrella checked out and maybe no network. That rules out relative links like
`../moonkey/...`, which need the siblings cloned at a known path.

The trick is to be honest about what the umbrella holds versus what it points to.

### It references repo summaries, it does not own them

A sibling's own CLAUDE.md is the authoritative summary of that repo: what it is,
its layout, conventions, gotchas. The umbrella must not keep a second hand-written
summary of each repo; that is just a copy of the CLAUDE.md, and it drifts. The
umbrella links to each sibling's CLAUDE.md (and its `llms.txt` if published), and
that link is the per-repo summary, by reference.

### It owns the integration view

What no sibling's CLAUDE.md covers is how the repos relate. Each describes itself
in isolation; nothing describes the connections. That is the umbrella's own
content, held locally so it stands alone:

- The **shared contracts** (a wire format, a status scale), owned here per "Single
  source of truth" above. Siblings reference up into the umbrella.
- A short **integration view**: who talks to whom, which repo provides and
  consumes which contract.

### The repo table is the map

The default registry is a markdown table in the umbrella's CLAUDE.md, the same
link-index pattern as a normal hub. One row per repo: a logical name, its source,
a link to its CLAUDE.md (and `llms.txt`), and a one-line role.

```markdown
| Repo             | Source                          | Docs                              | Role                  |
|------------------|---------------------------------|-----------------------------------|-----------------------|
| moonkey          | github.com/you/moonkey          | [CLAUDE.md](...), [llms.txt](...) | Connect IQ watch face |
| garmin-ai-status | github.com/you/garmin-ai-status | [CLAUDE.md](...), [llms.txt](...) | status aggregator     |
```

Refer to siblings by the logical name from this table ("moonkey: architecture"),
never by a local path. No YAML needed: an agent reads the table and follows the
links. A separate machine-readable file earns its place only when a script
consumes the list (see below).

### Integration notes, only when needed

When one sibling's cross-repo story is involved enough to need its own file, add
an integration note under the umbrella's `agent_docs/`. It is not a repo summary:
it records relationships (provides, consumes, how it integrates) and points to the
sibling's CLAUDE.md for everything internal. See
[templates/umbrella/agent_docs/integration-note.md](../templates/umbrella/agent_docs/integration-note.md).
Most ecosystems won't need these; the table plus the integration view in CLAUDE.md
is enough.

### If a script consumes the list

If you add a script (clone all siblings, generate offline digests, validate
links), give it a small machine-readable list instead of parsing the table:

```yaml
repositories:
  moonkey:
    url: github.com/you/moonkey
    path: vendor/moonkey       # only if you materialize
```

Keep one source of truth, not both: either the script parses the table, or it
reads this list and the table is generated from it. Don't pin a `version` by
default; a docs umbrella wants the current state of a sibling, not a frozen
commit. Add a pinned `version` only if the umbrella also has to describe a
specific compatible set, at which point it is a release manifest, not just docs.

### Reading a sibling's own docs is optional materialization

The map is always standalone. Reading a sibling's actual CLAUDE.md is a separate,
opt-in step. Pick by how often you need it and whether agents have network:

| Mechanism                    | Standalone for the map | Reading a sibling's docs needs | Drift risk             |
|------------------------------|------------------------|--------------------------------|------------------------|
| Link to sibling CLAUDE.md    | yes                    | a clone or checkout            | none; it is the source |
| Link to published `llms.txt` | yes                    | network, no clone              | none; it is the source |
| Generated digest (vendored)  | yes, fully offline     | nothing, checked in            | medium; regen cadence  |
| Git submodule                | yes after update       | nothing                        | none; heaviest         |

Linking is the baseline. A generated digest (a script clones each sibling and
writes a condensed copy of its CLAUDE.md and `llms.txt` into the umbrella) is the
fully-offline option; mark it generated and record what commit it came from.
Submodules are for when you actually edit siblings from the umbrella.

### Prior art

This is a known pattern: GitHub's polyrepo guidance calls the umbrella an
**integration layer** or **meta-repo**. The optional `repos.yaml` shape follows
multi-repo tools like vcstool (`.repos`) and Zephyr's `west.yml` (`url`, `path`,
and a `version` they use for pinning). Those tools pin for build reproducibility;
a docs umbrella tracks current state, so it skips pinning. The goal here is a
lightweight, markdown-first way to structure docs, not a service catalog or a
deployment graph.

### What to watch

- Integration-view drift: the umbrella's relationships and contracts can fall
  behind reality. A regeneration or review cadence keeps them honest.
- Don't let the table or a note grow into a copy of a sibling's CLAUDE.md. If you
  need the whole thing, link it or vendor a marked digest; don't paste it.
- If you do pin (the compatible-set case): check the pinned version still exists
  and how far behind HEAD it is.

## Cross-repo linking

Relative markdown links break across repo boundaries, so the "markdown links, not
backticks" rule needs a cross-repo variant. Options, cheapest first:

- **Reference by name and path**: "garmin-ai-status: `agent_docs/aggregator.md`".
  No tooling, but not clickable and not validated.
- **Vendor a shared docs directory** via git submodule or symlink so the path
  resolves locally. Clickable, but adds a dependency to manage.
- **Publish to a URL** and list it in `llms.txt` (see [scaling.md](scaling.md)).
  Stable, but the source of truth now lives outside the repo.

Pick by how tightly the projects are coupled; don't default to one blindly.

## Decision guide

Where a shared thing lives, by shape:

| Shared thing           | Monorepo                      | Siblings                      | Many surfaces                     |
|------------------------|-------------------------------|-------------------------------|-----------------------------------|
| Conventions / style    | root hub, children inherit    | shared skill                  | root hub                          |
| A contract / invariant | one owning `agent_docs/` file | owning repo, others reference | owning surface's doc, others link |
| Build orchestration    | root hub                      | per-repo, umbrella maps them  | root hub                          |
| Personal preferences   | `~/.claude/CLAUDE.md`         | `~/.claude/CLAUDE.md`         | `~/.claude/CLAUDE.md`             |

If you adopt only one rule: shared facts get exactly one owner; everyone else
references, nobody re-states. That covers drift across all three shapes.
