<!-- Umbrella hub for a set of sibling repos. It stands alone: it maps the repos and
     owns the cross-repo layer, and it links to each sibling's own CLAUDE.md rather
     than re-summarizing it. Refer to siblings by the logical name in the Repos
     table, never by a local path. -->

Guidance for agents working across these repos. Read this first. Each repo has its
own CLAUDE.md; this file maps how they fit together and owns the shared contracts.

## What this is

<One paragraph: the product or ecosystem these repos make up, and how they relate.>

## Repos

| Repo       | Source                | Docs                              | Role       |
|------------|-----------------------|-----------------------------------|------------|
| <repo-key> | github.com/you/<repo> | [CLAUDE.md](...), [llms.txt](...) | <one line> |

The link to each repo's CLAUDE.md is its summary. Don't restate it here.

## Shared contracts

Facts more than one repo must agree on. Owned here; repos reference up.

- **<contract>**: <one line>. Authoritative doc: [agent_docs/<contract>.md](agent_docs/<contract>.md).

## Integration view

Who provides and consumes what.

- <repo-key> provides <contract>; <other-repo> consumes it.
- <one line per significant connection>

## Conventions

- Refer to a sibling by its Repos-table name (`<repo-key>: <topic>`), not a path.
- <shared conventions; or "follow the <domain> skill" if there is one>
