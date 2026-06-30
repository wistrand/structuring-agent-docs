# Agent-facing writing style

Rules for prose inside CLAUDE.md and agent_docs/. Put a condensed version as a
"Documentation Style" block in CLAUDE.md so the rules apply every time an agent
edits docs or writes user-facing copy.

## Contents
- No AI-isms
- Links and tables
- Concise and assume competence
- Don't copy specific values out of code
- No time-sensitive content
- Consistent terminology

## No AI-isms

Drop marketing and filler. State the point directly.

Avoid:
- Superlatives and sales words: "the fastest way", "seamlessly", "powerful",
  "robust", "leverage", "blazing", "effortless".
- Rule-of-three cadences: "fast, simple, and reliable".
- "not just X, but Y" constructions.
- Filler preamble: "In this section, we will explore…".
- Hedging adjectives that add nothing: "very", "quite", "fairly".
- Jargon-as-flair ("load-bearing", "footgun", "blazing-fast"): say what you mean
  plainly instead. "critical invariants", not "load-bearing invariants".
- Em dashes as a tic: several per paragraph reads as AI. An occasional one is
  fine; overuse is the tell. No emojis in project copy.

Write what is true and what to do. A flat declarative sentence beats an
enthusiastic one.

## Links and tables

- Reference other docs with markdown links, not bare backtick filenames, and keep
  them one level deep. These are the "markdown links" and "one level deep" rules
  owned by SKILL.md "Core rules"; named here, stated in full there.

### Aligned tables

Pad table cells so columns line up in the raw markdown — the agent reads the
source, where an aligned table is scannable. If your editor or a formatter does
this automatically, let it; it isn't worth hand-aligning on every edit.

## Concise and assume competence

The agent is already capable. Add only what it can't infer: project-specific
names, rules, constraints, and the reasoning behind them. Cut explanations of
general concepts the agent already knows. Every paragraph should justify its
cost. If it restates something obvious, delete it.

## Don't copy specific values out of code

Constants — sizes, caps, timeouts, ports, version numbers — drift the moment the
code changes, and a confidently wrong number is worse than none. Name the
constant and where it lives ("the per-call cap in `entropy.ts`"), or state the
property qualitatively ("fill in chunks; a single oversized call throws"). Quote
a literal value only when it's a fixed external contract (an RFC field width, a
wire-format magic byte) that won't change, and say why it's fixed.

The same goes for pasted code: a copied function or signature drifts as silently as
a copied constant. Point at the source (`parseConfig()` in `config.ts`) instead of
reproducing it; paste a fragment only when it's illustrative and small, and treat
the source as authoritative.

## No time-sensitive content

Don't write "before August use the old API; after, the new one." It goes stale
and misleads. State the current method plainly. If history matters, put it under
an "Old patterns" / "Superseded" heading so it's clearly past, not current.

Don't put dates in filenames either; track history in git. (Generated data
snapshots are the exception, where the date is what makes each run immutable.
See agent-docs.md.)

## Consistent terminology

Pick one term per concept and use it everywhere. Don't mix "endpoint / URL /
route" or "field / box / element" for the same thing. Consistency lets an agent
match terms across files instead of guessing they're synonyms.
