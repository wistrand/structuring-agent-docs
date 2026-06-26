<!-- Subsystem deep-dive. One topic per file. Self-contained: an agent should be
     able to work this subsystem from this file alone. Add a table of contents
     once the file passes ~100 lines. -->

# <Subsystem> architecture

## Contents
- Overview
- Invariants
- Components
- Data flow
- Notes

## Overview

<What this subsystem does and where it sits in the whole. One paragraph. Link to
the top-level model if needed: [architecture.md](architecture.md).>

## Invariants

Subsystem-specific rules that must stay true. Global rules that span the whole
repo live in CLAUDE.md; link to them rather than restating them here.

- <e.g. "the pool is fixed-size; never reallocate mid-frame">

## Components

| Unit   | File      | Responsibility |
|--------|-----------|----------------|
| <name> | `src/...` | <what it owns> |

## Data flow

<Step the data through the subsystem. A short numbered list or ASCII diagram.>

1. <input> →
2. <transform> →
3. <output>

## Notes

<Anything an agent needs that isn't obvious from the code: chosen tradeoffs,
fallbacks, performance budgets. Move recurring traps to gotchas.md.>
