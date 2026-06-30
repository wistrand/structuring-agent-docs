<!-- Optional. Add one only when a sibling's cross-repo story is too involved for a
     row in the umbrella's Repos table plus the integration view. It is NOT a repo
     summary: it records how this repo connects to the others and points to the
     repo's own CLAUDE.md for everything internal. Most ecosystems need none. -->

# <repo-key> integration

> Repo summary lives in the repo's own CLAUDE.md: <git url> (and its published docs, if any).
> This note covers only how it connects to the rest of the ecosystem.

## Provides

Contracts this repo is the authority for (others depend on these):

- <contract>: <one line>

## Consumes

Contracts this repo depends on (owned by the umbrella or another repo):

- <contract>: owned by <umbrella | other-repo-key>

## How it integrates

<The cross-repo specifics: the calls, the shared format, the ordering or timing
that matters between this repo and the others. Omit anything internal to the repo,
which belongs in its own CLAUDE.md.>
