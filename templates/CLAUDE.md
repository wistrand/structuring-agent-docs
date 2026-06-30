<!-- Starter entry point. Delete sections that don't apply; keep it the cheap,
     always-loaded routing entry point. Move deep detail into agent_docs/ and link. -->

Guidance for agents working in this repo. Read this first, then the relevant
file in `agent_docs/`.

## What this is

<One paragraph: what the project does and how the pieces fit. An ASCII data-flow
diagram earns its space; save rationale prose for the README.>

## Layout

| Path          | Role                                    |
|---------------|-----------------------------------------|
| `src/`        | <what lives here>                       |
| `agent_docs/` | per-subsystem deep dives (linked below) |
| `docs/`       | generated assets (screenshots, icons)   |

## Commands

```bash
<dev>      # run locally
<test>     # run tests
<build>    # produce a build
<deploy>   # ship it
```

## Docs

- [agent_docs/architecture.md](agent_docs/architecture.md): components, data flow, the core model.
- [agent_docs/gotchas.md](agent_docs/gotchas.md): non-obvious traps. Skim before touching <risky area>.
<!-- add one line per agent_docs file, markdown link + one-line purpose -->

## Invariants

Global, cross-cutting rules that hold across the whole repo. Read before changing
the relevant subsystem. Keep subsystem-local rules in that subsystem's agent_docs
file instead. Don't state the same rule in both places, or the copies drift.

- <property that must stay true, e.g. "same input → same output, forever">
- <another>

## Conventions

- <code style: indent, quotes, line width>
- <what not to run / tooling constraints>
- <project-specific naming or layout rules>

## Documentation Style

- Markdown links for doc references you want an agent to follow, not backticks.
  Backticks are fine for source paths in tables and inline code. Align table columns.
- No AI-isms (no "powerful", "seamlessly", "leverage", rule-of-three, "not just
  X but Y"). No em dashes or emojis in project copy. State the point directly.
- Concise; assume the agent is competent. Add only what it can't infer (project
  names, rules, constraints, and the why). Cut explanations of general concepts.
- Keep this file the routing entry point; move subsystem detail into agent_docs/.
