<!-- Starter agent hub. Delete sections that don't apply; keep it the cheap,
     always-loaded routing layer. Move deep detail into agent_docs/ and link. -->

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

Load-bearing rules. Read before changing the relevant subsystem.

- <property that must stay true, e.g. "same input → same output, forever">
- <another>

## Conventions

- <code style: indent, quotes, line width>
- <what not to run / tooling constraints>
- <project-specific naming or layout rules>

## Documentation Style

- Markdown links for file references, not backticks. Align table columns.
- No AI-isms (no "powerful", "seamlessly", "leverage", rule-of-three, "not just
  X but Y"). No em dashes or emojis in project copy. State the point directly.
- Keep this file the routing layer; move subsystem detail into agent_docs/.
