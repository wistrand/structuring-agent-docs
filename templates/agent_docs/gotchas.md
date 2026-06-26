<!-- Non-obvious traps and the reasoning behind constraints, what the source code
     can't tell an agent. Append as you discover them. Two styles below; use
     either or both. Link to this file from CLAUDE.md with a "skim before X" hint. -->

# Gotchas and findings

## Contents
- Traps
- Findings

## Traps

Short warnings. One bold lead per trap, then the consequence and the fix.

- **<Surprising behavior in one line.>** <Why it bites and what to do instead.>
- **<Another trap.>** <…>

## Findings

Diagnosed bugs kept as case studies, so an agent understands why a constraint
exists and doesn't undo it.

### <Short title of the bug>

- **Symptom:** <what was observed>
- **Diagnosis:** <root cause>
- **Fix:** <what changed>
- **Takeaway:** <the rule that follows from this; often now an invariant>
