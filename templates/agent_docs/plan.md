<!-- Saved planning for work not built yet, or only partly. Its job is to keep an
     agent's planning from being lost between sessions. Lay out ordered phases and
     how each is verified. Track status as phases land. When the work is done,
     promote this into the subsystem's architecture-*.md and remove this file; note
     the move in CLAUDE.md. -->

# Plan: <topic>

> Status: <not started | in progress | mostly done>. Track per-phase below.

## Goal

<What this should achieve, and why. One paragraph.>

## Current state

<What exists today and what's missing: the gap this plan closes. Link the relevant
[agent_docs/architecture-<sub>.md](architecture-<sub>.md) so the starting point is
clear.>

## Approach

<The intended design and the reasoning behind it. Note alternatives considered and
why they were rejected, so the decision survives.>

## Testing methodology

<How the work is verified overall: the harness or commands, what "done" means, and
what must not regress. Each phase's "Verify" step refers back to this. State it
even if it's "no automated tests; check X by hand in the simulator".>

## Phases

Ordered so each phase is independently verifiable before the next starts.

### Phase 1: <name>

- [ ] <step>
- [ ] <step>

**Verify:** <the concrete check that this phase is done and correct: command to
run, behavior to observe, invariant that must still hold.>

### Phase 2: <name>

- [ ] <step>

**Verify:** <...>

## Open questions

<Decisions not yet made. Resolve before, or as part of, the phase that needs them.>

<!-- When all phases land: fold the design, decisions, and what each phase verified
     into architecture-<sub>.md, delete this file, and note it in CLAUDE.md
     ("plan-<topic> was promoted to architecture-<sub> (implemented)"). -->
