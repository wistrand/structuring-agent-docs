# agent_docs/ conventions

## Contents
- Naming by prefix
- Doc types
- Gotchas and findings
- Invariants
- Generated docs
- Generated data artifacts and fixtures
- Self-containment and cross-links

## Naming by prefix

The filename prefix signals the doc's type so an agent knows what it's opening
before reading. Kebab-case, descriptive, no version numbers or dates in the name.
(The one exception is generated data artifacts, which are dated on purpose. See
"Generated data artifacts and fixtures" below.)

| Prefix                 | Holds                                      | Lifecycle                            |
|------------------------|--------------------------------------------|--------------------------------------|
| `architecture-<sub>`   | how a subsystem works now                  | sync with code                       |
| `design-<topic>`       | spec + rationale for a format/feature      | sync with code                       |
| `plan-<topic>`         | saved planning, not built or partly built  | promote to architecture-* when built |
| `research-<topic>`     | investigations, citations, options weighed | mostly static                        |
| `gotchas` / `findings` | traps and diagnosed bugs                   | append as found                      |
| `settings`             | reference derived from source              | GENERATED                            |
| `store-listing`        | user-facing release copy                   | sync with release                    |

For a single small subsystem one `architecture.md` is enough. Split into
`architecture-audio.md`, `architecture-webgl.md`, etc. when the combined file
gets unwieldy or the subsystems are independently editable.

## Doc types

- **architecture-\***: reference. Module responsibilities, data flow, the
  mental model, concrete invariants for that subsystem. Dense; assumes the agent
  knows the language and domain. This is what replaces reading the source.
- **design-\***: a spec with rationale: a JSON schema, a file format, the math
  behind an algorithm, and why it's shaped that way.
- **plan-\***: saved planning for work not built yet, or only partly. Goal, gap,
  the approach, ordered phases, and a testing methodology. Its job is to keep an
  agent's planning from being lost between sessions, so the work can resume or be
  reviewed. Lay out clear phases, each with how it is verified before the next
  starts, so an agent resuming mid-plan knows what is proven and what is next.
  When the work lands, promote it (see below) rather than delete the thinking.
- **research-\***: what was investigated: techniques, papers, measured impact,
  options weighed and rejected. Explains why the current approach won.

### Promoting a plan to architecture

A `plan-*` doc describes intended work; an `architecture-*` doc describes how the
code works now. When a plan is implemented, that distinction is the lifecycle:
rewrite the plan into the subsystem's `architecture-*.md` (the durable record of
current behavior), then remove the `plan-*` file. The planning and the decisions
carry forward into the architecture doc; they aren't thrown away.

- Partly implemented: keep the `plan-*` file and mark which parts are done, so an
  agent knows what is real versus still intended. Move the landed parts into
  `architecture-*` as they ship.
- Note the move in CLAUDE.md so an agent doesn't hunt for the old plan: "`plan-x`
  was promoted to `architecture-x` (implemented)."

## Gotchas and findings

The highest-value doc for an agent, because it holds what source code cannot
show: non-obvious traps and the reasoning behind constraints.

Two styles, use either or both:

- **gotchas.md**: short bolded warnings. "Rate limiting is in-memory, so
  per-isolate." "makeWebRequest needs real HTTPS, no redirects."
- **findings.md**: case studies of real bugs, each as
  symptom → diagnosis → fix → takeaway. Preserves *why* a constraint exists so
  an agent doesn't undo it.

A filled `findings.md` entry looks like this:

```markdown
## makeWebRequest silently truncates bodies over ~64 KB

**Symptom:** the weather widget showed stale data every few hours, no error logged.
**Diagnosis:** Connect IQ caps response bodies at ~64 KB and returns the
truncated JSON with a 200, so the parse succeeded on a partial object.
**Fix:** request the compact endpoint; drop the `hourly` block server-side.
**Takeaway:** treat any `makeWebRequest` body as size-capped. A 200 does not
mean a complete payload.
```

This earns its place as prose because the cap is a platform quirk with no cheap
test to assert it. That is the bar from SKILL.md's "What belongs in a doc": if a
finding could be a test, add the test; write it up only when it can't.

Reference these from `CLAUDE.md` with a "skim before touching X" hint.

## Invariants

Load-bearing properties that must stay true. An agent reads them before changing
a subsystem so it knows what a careless edit silently breaks. Examples:

- "Same input → same pixels forever" (determinism for URL hashing)
- "The import graph is acyclic; a cycle test enforces it"
- "The watch payload must stay tiny; bars ship as a digit string, not arrays"

Put cross-cutting invariants in `CLAUDE.md`; put subsystem-specific ones at the
top of that subsystem's `architecture-*.md`. State them as flat assertions, not
suggestions. A filled `Invariants` section at the top of `architecture-render.md`:

```markdown
## Invariants

- Same input → same pixels, forever. The render is a pure function of the seed,
  and URL hashing depends on it. Changing the output for an existing seed is a
  breaking change, never a bugfix.
- The import graph is acyclic. `deno task test:cycles` enforces it; an import
  that closes a loop fails CI.
```

## Generated docs

Any doc derivable from source should be generated, never hand-maintained, so it
can't drift. Typical: a settings reference generated from a settings schema, or
CLI help captured from `--help`.

- Drive it from a build target (`make settings-doc`).
- Mark the file as generated at the top so no one edits it by hand.
- Note the generator in `CLAUDE.md` commands.

## Generated data artifacts and fixtures

When a project has a data pipeline or LLM-synthesis step, `agent_docs/` can also
stage its outputs (clustering reports, sampled data, audit trails) so an agent
has real fixtures, not just descriptions. Date these filenames
(`cluster-report.2026-05-25.md`) — the one deliberate exception to "no dates in
filenames" — so each run is immutable and snapshots accumulate as diffable
history. Document the naming where the generator lives and link the pipeline doc
from CLAUDE.md, not each dated file; pruning is manual. These are inputs and
outputs, not instructions.

## Self-containment and cross-links

Each file should stand alone: an agent editing one subsystem reads that one file
without first reading four others. Some shared context (a pointer to
`architecture.md` for the overall model) is fine.

Cross-links between agent_docs are useful extras, but every file must also be
reachable directly from `CLAUDE.md`; don't bury a file so it's only findable by
following a chain. Agents preview chained files with `head` and miss content.
