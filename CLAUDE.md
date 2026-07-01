# Working in this repo

Read this first when editing the skill. This file is the agent's entry point for
maintaining it, auto-loaded each session (`AGENTS.md` symlinks here).

## Layout

- `SKILL.md` is the skill itself: model, core rules, and the index into `references/`.
- `references/` holds the skill's deep dives: `claude-md`, `agent-docs`, `writing-style`, `advanced`.
- `templates/` holds starter files an adopter copies into their own repo.
- `README.md` is the human-facing overview and install; not loaded as the skill.
- `docs/` + `.github/workflows/` handle publishing: build the Pages site and a
  payload-only skill zip (build output, not part of the skill).
- `benchmark/` is a dependency-free Node harness that measures the blast-radius
  claims via an LLM API (OpenRouter). Repo tooling, not shipped in the skill zip.

The repo dogfoods the model it teaches: `README.md` for humans, this `CLAUDE.md`
for the agent working here, `SKILL.md` + `references/` as the product.

**The "no CI" rule is narrow: the docs skill provides no code**, neither to an agent
using it nor as advice to a project. It says nothing about this repo's
build/publish CI (allowed, like any project's) or a project that uses the skill
(free to run whatever CI it wants). The zip ships `SKILL.md` + `references/` +
`templates/` and nothing executable; that's the whole rule.

## Discipline when editing

- **Keep the skill lean. It teaches lean docs, so it must practice them.** This is
  the governing constraint: a bloated skill refutes its own thesis. Every addition
  earns its place against the skill's own writing-style rules (concise, assume
  competence, every paragraph justifies its cost). Prefer cutting to adding; when
  you add a paragraph, find one to remove. State each rule once and point to it
  (see single-owned rules below); resist the urge to re-explain.
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
  sync table. If a satellite starts re-explaining a rule, collapse it back to a
  pointer.
- **Project rules go in these docs, not the agent's memory system.** This repo
  dogfoods the skill, so the entry point is the single owner of project constraints.
  Recording a rule in per-project memory both fails to dogfood and makes a second
  copy that drifts from this file, exactly what the skill warns against. Write it
  here.

## Trigger examples

Should fire:

- "Help me set up a CLAUDE.md for this repo." Core case.
- "My CLAUDE.md is 900 lines and unwieldy; how do I split it?" Too-large entry point.
- "How should I organize agent_docs / AGENTS.md so Claude navigates the repo?"
  Agent-navigation framing.
- "We have a monorepo, how do we document it for coding agents?" Multi-project,
  still in scope.

Should NOT fire (doc-adjacent but out of scope):

- "Write the README intro for my library." Human-facing prose.
- "Add JSDoc/docstrings to these functions." In-code API docs.
- "Draft a blog post about our architecture." Narrative for people.
- "Generate API reference docs from my OpenAPI spec." Doc-generation tooling.

The split is the test: in scope when the ask is about *how an agent picks up repo
context*; out of scope when it's human-facing prose or code-level docs.

## Deliberate non-goals

Considered and left out on purpose; don't "fix" these without a demonstrated need:

- **No commit-message convention.** The diffs are the log, so a short subject line
  is enough. No structured format, body, or CHANGELOG.
- **No shipped or prescribed link validation.** The skill carries no validator and
  names no tool for checking links; the agent confirms them while editing, by
  whatever means it chooses. Shipping executable validation would change what the
  skill is: an agent loads it for advice on structuring docs, not to run code.
  Settled choice, not a "revisit if rot gets bad."
- **No worked example project.** A fake repo with filled docs is the most
  drift-prone artifact possible. The templates plus inline filled excerpts (the
  `findings.md`, `Invariants`, and generated-doc examples in
  [references/agent-docs.md](references/agent-docs.md), the link-index block in
  [references/claude-md.md](references/claude-md.md)) carry the concreteness
  without the maintenance burden.

## Considering (not yet acted on)

A decision queue, not a wishlist: each item lands in the skill or moves to
"Deliberate non-goals" once decided. Promote to its own `agent_docs/` file only if
the list outgrows this entry point.

- **Personal vs team-shared agent docs.** A one-line note on `CLAUDE.local.md`, the
  Claude Code binding for gitignored personal rules. Act if adopters ask where
  personal rules go.
- **Move the benchmark to its own repo.** `benchmark/` is the only executable code here;
  splitting it out would make this repo payload-pure, so even a repo-root symlink install
  exposes no code. Act if the tooling-in-repo purity concern outweighs keeping the
  benchmark beside the skill it tests.
