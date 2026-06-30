<!-- Umbrella README for a set of sibling repos: the human-facing map. It says what
     the set is and links each repo by URL, never a relative path, since a human may
     read this on the web or without the repos checked out locally. Don't restate a
     repo's own README; link to it. Keep agent rules out of here; those live in the
     umbrella CLAUDE.md. Add a quick start only if these repos form a product run
     together. Delete what doesn't apply. -->

# <Umbrella name>

<One line: what this set of repos is. A product, a platform, or a working set.>

<One paragraph: why these repos are grouped and how they relate. If they share a
product or a contract, say so; if they are an independent working set, say that.>

## Repos

| Repo                                        | What it is |
|---------------------------------------------|------------|
| [<repo-key>](https://github.com/you/<repo>) | <one line> |

Each repo has its own `README.md` (humans) and `CLAUDE.md` (agents). Link to a repo's
README for depth; don't restate it here.

## For agents

Start at [CLAUDE.md](CLAUDE.md): it maps the repos and owns the cross-repo layer.
