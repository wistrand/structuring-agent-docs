# agent_docs/ conventions

## Contents
- Naming by prefix
- Doc types
- Point into the source
- When a doc and the code disagree
- Keeping docs current
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

The table is the common set, not a closed list. Add a domain-specific prefix when
a project needs one (`store-listing` for release copy, `runbook-*` for ops).

For a single small subsystem one `architecture.md` is enough. Split into
`architecture-audio.md`, `architecture-webgl.md`, etc. when the combined file
gets unwieldy or the subsystems are independently editable.

A concern that cuts *across* subsystems (auth, i18n, logging, theming, error
handling) doesn't fit any one `architecture-<sub>` file. Give it its own
`architecture-<concern>.md` (or `design-<concern>.md`) as the single owner, and
have the subsystem docs link to it rather than each re-describing it. The prefix
names a subsystem or a cross-cutting concern; both are valid axes.

## Doc types

- **architecture-\***: reference. Module responsibilities, data flow, the
  mental model, concrete invariants for that subsystem. Dense; assumes the agent
  knows the language and domain. This is what replaces reading the source, so it
  points *into* the source: each piece names the file (and key symbol) that
  implements it (see "Point into the source" below).
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

## Point into the source

A doc that says what a subsystem does should also say where each piece lives in the
code: the file path, plus the key symbol when it helps (`getTier()` in
`src/detect.ts`, not just `src/detect.ts`). This is the source-file target type of
the link-index pattern ([claude-md.md](claude-md.md)): the doc becomes an index into
the source, so the agent reads the prose and jumps straight to the right file and
function instead of grepping. The prose is the map; the path-plus-symbol is the
coordinate that makes "replaces reading the source" real.

"Source" here is whatever holds the real thing, not only code: a file and symbol in
a codebase, but a chapter in a book, a clause in a contract, a column in a dataset.
Point at that authoritative artifact in whatever form addresses it; the code
examples here are just the most common case.

This lives in the CLAUDE.md **Layout** table (top-level paths → roles), an
architecture doc's **Components** table or a short **Key files** list (each unit →
its file and responsibility), and inline wherever you name where a behavior is
implemented. Use plain backtick paths, not markdown links. They point at code the
agent opens directly, not docs to follow. Name the symbol too: it stays greppable
when a file moves, which softens the one cost here: a renamed file leaving a stale
path.

Index authoritative source only; skip what's derived, vendored, or `.gitignore`d.
Treat a gitignore hit as "probably derived or dependency: reason about *why* it's
ignored," not an automatic omit. Two catches: a *committed* generated file is still
derived (mark it generated, point at its producer, not the output); and a gitignored
config/secret (`.env.local`) is a signal to document its *requirement* as a gotcha
("needs `API_KEY`, `DB_URL`; uncommitted"), never its contents, which drift and leak
if the docs are published.

## When a doc and the code disagree

Docs drift; the code is what runs. Split authority: the code is authoritative for
*what the system does now*, the doc for *why* it's that way and what's intended. On
a conflict about current behavior, trust the code and fix the doc. Never reshape
working code to match a stale doc. This is the fine print on "replaces reading the
source": it holds only while the doc is current, which is why the durable docs are
the *why* (rarely changes) and the generated ones, not blow-by-blow narration of
code that does.

## Keeping docs current

Structure is worthless if the docs drift, and the skill can't enforce upkeep, so
make it a habit, cheapest moment first:

- **Update as you change code.** A doc that points at `src/x.ts` is the doc to
  revisit when you touch `src/x.ts`. The index runs in reverse. Fixing it in the
  same change is the cheapest correction and the least drift; the agent should do
  this without being asked.
- **Sweep on demand.** When inline updates lag, after a big or cross-cutting
  change, "update the docs" (or "update all docs") triggers a deliberate pass.
  Make it a reconciliation, not a rewrite:
  - check each doc against the current code; the code wins on conflict (never
    reshape working code to match a stale doc);
  - regenerate generated docs instead of hand-editing them;
  - fix the cheap structural things: links resolve, moved files noted, a landed
    `plan-*` promoted to `architecture-*`, invariants still true;
  - leave the *why* unless the reason itself changed. It's the most durable
    content and the easiest to wreck with a careless rewrite.
- **Capture corrections as they happen.** When the agent makes a mistake or hits a
  trap that wasn't written down, distill the fix into one durable rule so it isn't
  repeated: a recurring, non-obvious gotcha goes in `gotchas`/`findings`, a
  cross-cutting rule in CLAUDE.md, stated as a flat always/never. This is how the
  gotchas/findings doc fills (SKILL.md "Capture the why"). Same bar applies: if a
  test, type, or lint can enforce it, add that instead of a doc line, and never add a
  second copy of a rule that already has an owner. Prune a rule once code enforces it
  or it goes stale. Some toolchains automate the capture: Claude Code can fold the
  correction into CLAUDE.md from a PR comment.

Prefer small corrections to confident rewrites. If the project wants this reliable,
document the trigger in its entry point ("to refresh docs after a big change, ask
the agent to update the docs") so every agent knows the convention.

## Gotchas and findings

This doc holds what source code cannot show: non-obvious traps and the reasoning
behind constraints.

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

Critical properties that must stay true. An agent reads them before changing
a subsystem so it knows what a careless edit silently breaks. Examples:

- "Same input → same pixels forever" (determinism for URL hashing)
- "The import graph is acyclic; a cycle test enforces it"
- "The watch payload must stay tiny; bars ship as a digit string, not arrays"

Place them per SKILL.md "Core rules": cross-cutting in `CLAUDE.md`,
subsystem-specific at the top of that subsystem's `architecture-*.md`, never both.
State them as flat assertions, not suggestions. A filled `Invariants` section at the top of `architecture-render.md`:

```markdown
## Invariants

- Same input → same pixels, forever. The render is a pure function of the seed,
  and URL hashing depends on it. Changing the output for an existing seed is a
  breaking change, never a bugfix.
- The import graph is acyclic. `deno task test:cycles` enforces it; an import
  that closes a loop fails CI.
```

## Generated docs

The "Generate, don't drift" rule (SKILL.md "Core rules") applied to `agent_docs/`:
any doc derivable from source is generated, never hand-maintained, so it can't
drift. Typical: a settings reference generated from a settings schema, or CLI help
captured from `--help`.

- Drive it from a build target (`make settings-doc`).
- Mark the file as generated at the top so no one edits it by hand.
- Note the generator in `CLAUDE.md` commands.

## Generated data artifacts and fixtures

When a project has a data pipeline or LLM-synthesis step, `agent_docs/` can also
stage its outputs (clustering reports, sampled data, audit trails) so an agent
has real fixtures, not just descriptions. Date these filenames
(`cluster-report.2026-05-25.md`), the one deliberate exception to "no dates in
filenames", so each run is immutable and snapshots accumulate as diffable
history. Document the naming where the generator lives and link the pipeline doc
from CLAUDE.md, not each dated file; pruning is manual. These are inputs and
outputs, not instructions.

## Self-containment and cross-links

Each file should stand alone: an agent editing one subsystem reads that one file
without first reading four others. Some shared context (a pointer to
`architecture.md` for the overall model) is fine.

Cross-links between agent_docs are useful extras, but every file must also be
reachable directly from `CLAUDE.md`; don't bury a file so it's only findable by
following a chain. Deeply nested files get skimmed or missed.
