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
  `references/` or `templates/`, grep for inbound links, fix them, and confirm
  every deep dive is still linked from `SKILL.md`:

  ```bash
  grep -rn "(references/\|templates/" SKILL.md references README.md
  ```

  Only prose links must resolve; illustrative paths in fenced or inline code are
  exempt.
- **Re-check the trigger examples after editing the `description`.** The
  `description` frontmatter in `SKILL.md` is the only thing deciding when the skill
  auto-loads. Confirm the should-fire / should-not-fire lists below still hold.
- **Keep duplicated rules in sync.** A few rules (one level deep, the `AGENTS.md`
  alias, generate-don't-drift, single-owner shared facts) are restated across
  reference files as deliberate reinforcement, with `SKILL.md` authoritative. If
  the model changes, grep the rule and update every copy together. Don't grow a
  formal sync table; if a rule needs that much bookkeeping, collapse the satellites
  to a link to `SKILL.md`.

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
- **No link-checker script.** A link-existence check would be cheap as CI, but the
  link set is small and the grep above catches most rot by hand. Revisit if rot
  becomes recurring in practice.
- **No worked example project.** A fake repo with filled docs is the most
  drift-prone artifact possible. The templates plus inline filled excerpts (the
  `findings.md` and `Invariants` examples in
  [references/agent-docs.md](references/agent-docs.md), the link-index block in
  [references/claude-md.md](references/claude-md.md)) carry the concreteness
  without the maintenance burden.
