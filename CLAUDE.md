# Working in this repo

Read this first when editing the skill. The deliverable is `SKILL.md` plus
`references/` and `templates/`; this file is the hub for an agent maintaining
them, auto-loaded each session. `AGENTS.md` symlinks here. `README.md` is the
human-facing overview (what the skill is, how to install) and holds the file tree.

This repo is itself an instance of the model the skill teaches: `README.md` for
humans, this `CLAUDE.md` for the agent working here, `SKILL.md` + `references/` as
the product.

## Discipline when editing

- **Cross-file links are the main rot risk.** When you rename or move a file under
  `references/` or `templates/`, confirm every prose link that pointed at it still
  resolves, fix the ones that broke, and confirm every deep dive is still linked
  from `SKILL.md`. Only prose links must resolve; illustrative paths in fenced or
  inline code are exempt.
- **Re-check the trigger examples after editing the `description`.** The
  `description` frontmatter in `SKILL.md` is the only thing deciding when the skill
  auto-loads. Confirm the should-fire / should-not-fire lists below still hold.
- **Rules are single-owned; satellites are pointers, not copies.** The recurring
  rules (one level deep, markdown links, the `AGENTS.md` alias, generate-don't-drift)
  are stated once in `SKILL.md` "Core rules"; single-owner shared facts is stated
  once in `advanced.md`. Where a reference file needs one, it names the rule and
  points to the owner instead of re-explaining it, so there is no second copy to
  drift. If you change an owned rule, edit the owner; the pointers carry no
  substance, so they don't need syncing. Don't reintroduce full restatements or a
  sync table — if a satellite starts re-explaining a rule, collapse it back to a
  pointer.

## Trigger examples

Should fire:

- "Help me set up a CLAUDE.md for this repo." — core case.
- "My CLAUDE.md is 900 lines and unwieldy; how do I split it?" — too-large-hub.
- "How should I organize agent_docs / AGENTS.md so Claude navigates the repo?" —
  agent-navigation framing.
- "We have a monorepo — how do we document it for coding agents?" — multi-project,
  still in scope.

Should NOT fire (doc-adjacent but out of scope):

- "Write the README intro for my library." — human-facing prose.
- "Add JSDoc/docstrings to these functions." — in-code API docs.
- "Draft a blog post about our architecture." — narrative for people.
- "Generate API reference docs from my OpenAPI spec." — doc-generation tooling.

The split is the test: in scope when the ask is about *how an agent picks up repo
context*; out of scope when it's human-facing prose or code-level docs.

## Deliberate non-goals

Considered and left out on purpose; don't "fix" these without a demonstrated need:

- **No commit-message convention.** The diffs are the log, so a short subject line
  is enough — no structured format, body, or CHANGELOG.
- **No shipped or prescribed link validation.** The skill carries no validator and
  names no tool for checking links; the agent confirms them while editing, by
  whatever means it chooses. Shipping executable validation would change what the
  skill is: an agent loads it for advice on structuring docs, not to run code.
  Settled choice, not a "revisit if rot gets bad."
- **No worked example project.** A fake repo with filled docs is the most
  drift-prone artifact possible. The templates plus inline filled excerpts (the
  `findings.md` and `Invariants` examples in
  [references/agent-docs.md](references/agent-docs.md), the link-index block in
  [references/claude-md.md](references/claude-md.md)) carry the concreteness
  without the maintenance burden.
