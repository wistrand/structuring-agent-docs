# Hacker News thread (simulated)

A project analysis written in the form of a Hacker News discussion. The personas
are invented; every technical point is grounded in the actual contents of this
repo (`SKILL.md`, `references/`, `templates/`, `MAINTAINING.md`). Treat it as a
critique dressed up as a comment section, not a real thread.

---

**Show HN: A skill that structures your repo's docs so coding agents navigate them (github.com)**
*287 points by ewistrand 9 hours ago | flag | hide | 142 comments*

> It's a Claude Code skill, not a library. Three tiers: README.md for humans,
> CLAUDE.md as an agent "read-first" hub, and agent_docs/*.md deep dives that
> only load when a task touches them. Progressive disclosure applied to a whole
> repo instead of a single SKILL.md. No code, just prose + templates.

---

▲ **tptacek** 8 hours ago [−]

The core insight here is just RAG-by-filesystem, and I mean that as a
compliment. Instead of embedding your docs and hoping cosine similarity pulls
the right chunk, you make the *directory layout* the retrieval index and let
the agent do the routing. The "CLAUDE.md is the cheap routing layer, agent_docs
costs nothing until you follow a link" framing is the whole pitch and it's
correct: you're paying tokens for a table of contents, not the library.

What I can't tell from the README is how much of the benefit is the structure
vs. just *having written the docs at all*. The hard part was never the folder
names.

  ▲ **ewistrand (OP)** 8 hours ago [−]

  Author here. You're right that writing the docs is the work. The skill's claim
  is narrower: given that you're going to write them, here's a layout that
  doesn't blow the context window every session. The load-bearing rule is "one
  level deep" — every agent_docs file links straight from CLAUDE.md, no
  doc→doc→doc chains — because agents preview nested files with `head` and miss
  the body. That's the part people get wrong, not the existence of docs.

    ▲ **majke** 7 hours ago [−]

    > agents preview nested files with `head` and miss the body

    This is the most interesting empirical claim in the whole thing and it's
    asserted, not shown. If it's true it's a real constraint worth designing
    around. If it's a 2025-vintage behavior that the next model release fixes,
    you've baked a workaround into a methodology. I'd want a date stamp on that
    observation at minimum.

      ▲ **ewistrand (OP)** 7 hours ago [−]

      Fair. It's behavioral, not a law. The repo's own writing-style ref actually
      bans time-sensitive content in the docs, so dating it would be ironic, but
      you're pointing at a real tension: a methodology built on current model
      quirks has a shelf life the methodology doesn't admit to.

▲ **simonw** 8 hours ago [−]

The thing I keep coming back to with these schemes is the AGENTS.md vs CLAUDE.md
fragmentation. This repo's answer is to symlink `AGENTS.md → CLAUDE.md` so every
tool reads one source of truth. That's the pragmatic move and I do it too, but
let's be honest that we're papering over a standards gap with a symlink. The
day two tools want *different* content under those two names, the symlink is a
landmine, not a bridge.

  ▲ **cmckn** 7 hours ago [−]

  The symlink also doesn't survive a lot of Windows checkouts and half the CI
  tarball tooling. "One source of truth" quietly becomes "one source of truth on
  POSIX with core.symlinks=true." Minor, but it's the kind of thing that bites
  the exact junior dev the docs were supposed to help.

    ▲ **ewistrand (OP)** 7 hours ago [−]

    Both real. The alias is the least-bad option today, not a victory. If the
    AGENTS.md standard consolidates, the right move is to flip ownership — make
    AGENTS.md the file and CLAUDE.md the alias — without touching the model.

▲ **jasode** 7 hours ago [−]

Counterpoint nobody wants to hear: docs written *for the agent* are a code
smell. If your codebase needs a hand-maintained CLAUDE.md explaining its
invariants and gotchas before an agent can touch it safely, the invariants
should be types, the gotchas should be tests, and the "why" should be in the
commit that introduced the constraint. You're building a parallel documentation
universe that drifts from the code by construction.

  ▲ **pclmulqdq** 6 hours ago [−]

  This is the strongest comment in the thread and it's underrated. Every
  "capture the why" doc is a comment that outlived the line it described. The
  repo even has a whole "generate, don't drift" rule — which is them conceding
  your point for the subset of docs that *can* be generated, and then writing
  prose for the subset that can't, which is exactly the subset most likely to go
  stale.

    ▲ **ewistrand (OP)** 6 hours ago [−]

    I half agree. The "generate, don't drift" rule exists precisely because
    hand-maintained derived docs (CLI flags, settings tables) are the first to
    rot — so push those into a build target. What's left is decision history:
    "we rejected a link-checker script because shipping executable code in a
    docs skill changes what it is." A type can't hold that. A test can't hold
    that. The commit *can*, but nobody greps 400 commits; they read one
    gotchas file. The drift risk is real, it's just cheaper than the
    archaeology.

      ▲ **jasode** 6 hours ago [−]

      "Cheaper than the archaeology" is an honest answer and more than most of
      these projects give. I still think you're one model generation away from
      the agent just reading the commit, but I'll take the concession.

        ▲ **ewistrand (OP)** 1 hour ago [−]

        Coming back to this thread because the critique stuck — pushed a new
        "What belongs in a doc" section to address it head-on. The rule now leads
        with *prefer executable enforcement*: if it can be a type, make it a type;
        a test, a test; generated, generate it. A doc that restates what a type
        or test already guarantees is a smell — delete it and write the test. So
        your "invariants want to be types, gotchas want to be tests" point is now
        rule #1, not a rebuttal.

        Where I think you're wrong is the "one generation from reading the commit"
        bet, and it's not about model strength. The doc isn't compensating for the
        *code's* inadequacy — it's compensating for the *agent's amnesia*. An agent
        has no memory between sessions: the platform trap it diagnosed today is
        gone tomorrow unless it writes it down. "The why is in the commit" is true
        and unreachable — an agent editing one file doesn't blame every line and
        read each introducing commit. A findings doc is that why, curated and put
        where the next agent reads first. That's the line: documentation as a code
        smell vs. documentation as a working note for a reader with no long-term
        memory.

          ▲ **jasode** 50 minutes ago [−]

          The amnesia framing is the first version of this argument I can't
          wave away. "Compensating for the reader's lack of memory, not the
          code's inadequacy" is a real distinction and it's the right axis. I'd
          still push: a curated findings doc *is* a cache, and caches go stale —
          the moment the trap stops being true, the note is now actively lying to
          the next session, with more authority than a commit because it sits
          where the agent reads first. You've moved the failure mode, not removed
          it. But "moved to a place where a human can cheaply prune it" is a
          better place than I gave you credit for. Concede the frame, keep the
          stale-cache worry.

            ▲ **pclmulqdq** 35 minutes ago [−]

            Worth flagging the agent_docs ref grew a matching line — the
            payload-cap finding now says it earns prose *because there's no cheap
            test to assert it*, and explicitly tells you to add the test when one
            exists. That's the rule policing its own examples, which is the part
            that makes me believe the section isn't just a rhetorical patch over
            this thread.

▲ **swyx** 6 hours ago [−]

Meta-observation: this is a skill that teaches an AI how to write docs so that
an AI can read them. We have fully closed the loop. The writing-style ref even
has a "No AI-isms" section banning "seamlessly," "robust," "leverage," rule-of-
three cadences, and em-dash tics — i.e. an AI being instructed not to write like
an AI, by an AI, for review by an AI. I'm not even being snarky, I think this is
genuinely where things are going, but somebody should sit with that for a second.

  ▲ **minimaxir** 6 hours ago [−]

  The "No AI-isms" list is the most useful single file in the repo and it has
  nothing to do with agents. It's a style guide that would improve 90% of human
  technical writing. Banning "not just X, but Y" alone would save the internet.

  ▲ **dang** 5 hours ago [−]

  We detached this subthread from a flamewar about whether LLMs can write. The
  on-topic part — the style guide — is good; please keep it there.

▲ **the_duke** 7 hours ago [−]

I like that the MAINTAINING.md documents the things they *deliberately didn't
build*: no link-checker script, no worked example project, no commit-message
convention. That's rarer and more valuable than the positive rules. Most repos
tell you what they did; almost none tell you what they considered and rejected
and why, which is the exact information that stops the next contributor from
"helpfully" adding the thing back.

  ▲ **wpietri** 6 hours ago [−]

  Strong agree. The "no example project" rationale is the sharpest: a fake repo
  is the most drift-prone artifact possible because its docs have to stay
  consistent with both themselves *and* the evolving rules — which is the precise
  failure mode the skill warns about. Refusing to ship the demo because the demo
  would violate the thesis is a level of discipline I rarely see.

    ▲ **throwaway_docs** 5 hours ago [−]

    Counterpoint: I bounced off it in 60 seconds *because* there's no example. I
    don't want to assemble the gestalt from a SKILL.md plus six reference files;
    I want to see one filled-in repo and copy it. "Inline excerpts next to the
    rule" is principled and also more work for the reader than a worked example
    would have been. Discipline for the author can be friction for the user.

      ▲ **the_duke** 5 hours ago [−]

      That's the real tradeoff and I think reasonable people land on both sides.
      The templates/ dir is the compromise — skeletons without a fake codebase
      around them. Whether that's enough scaffolding depends on whether you learn
      from structure or from example.

▲ **nostrademons** 6 hours ago [−]

The token-economics argument deserves more scrutiny than it's getting. The claim
is CLAUDE.md stays cheap and agent_docs loads on demand. But "on demand" assumes
the agent reliably (a) notices the link, (b) decides it's relevant, and (c)
actually opens it before acting. Miss (c) and you get the worst case: a confident
edit made *without* the deep-dive that existed two clicks away. A monolithic
file is more tokens but zero retrieval misses. This is a recall-vs-cost tradeoff
dressed as a free lunch.

  ▲ **ewistrand (OP)** 6 hours ago [−]

  This is the best technical objection here. You're right it's recall-vs-cost,
  not free. The bet is that for a *large* doc set the monolith stops fitting the
  window at all, so 100% recall on a file you can't load is worth less than 80%
  on a file you can. The "one level deep" and "one topic per file, self-
  contained" rules are both there to push retrieval recall up — if the right doc
  is one hop from the hub and needs no prerequisites, miss-rate drops. It doesn't
  go to zero. For a small repo your monolith is genuinely the better call, and
  the skill says as much (the "thick hub" shape is a single file on purpose).

    ▲ **nostrademons** 5 hours ago [−]

    Appreciate that you didn't pretend it was free. The "thick hub for small
    repos" carve-out is the tell that the method is honest about its own
    boundaries. Most of these don't have a "just use one file" mode.

▲ **zydeco** 5 hours ago [−]

Has anyone actually measured this? Every claim in here is mechanism-plausible
and evidence-free. "Agents navigate more efficiently" — efficiently by what
metric, on what task suite, vs. what baseline? I'd kill for a benchmark: same
repo, three layouts (no docs / monolith / this), N tasks, measure tokens-to-
correct-edit and error rate. Until then it's a well-argued aesthetic.

  ▲ **ewistrand (OP)** 5 hours ago [−]

  No benchmark, and that's the honest gap. It's distilled from one person's
  daily agent use, not an eval. I'd love the study you're describing; the hard
  part is that "tokens-to-correct-edit" depends enormously on the repo and the
  model, so a clean number for one project doesn't transfer. Doesn't excuse the
  absence of *any* measurement though. Fair hit.

▲ **kragen** 4 hours ago [−]

The part I find quietly radical is treating documentation as a *graph with a
fixed depth bound* rather than a tree or a pile. "One level deep, markdown links
not @-imports, because @-imports load eagerly and defeat the routing hub" is a
genuinely precise distinction and most people writing CLAUDE.md files have no
idea their import syntax is silently pulling the whole file into context every
session. That one rule probably justifies the repo.

  ▲ **ot** 4 hours ago [−]

  Yeah, the @-import footgun is the single most actionable thing here. It's the
  difference between "I have a nice hub file" and "I have a nice hub file that
  eagerly inlines 4,000 lines of deep-dives on every turn and wonders why it's
  slow." Worth the visit for that alone.

▲ **wsxcde** 3 hours ago [−]

Nitpick that's actually a real concern: the duplicated-rules table in
MAINTAINING.md. They deliberately restate some load-bearing rules across
reference files "so each reads alone," then maintain a change-set table mapping
each rule to its owner + satellites. That's a manual consistency-management
system for a project whose entire thesis is "generate, don't drift / single
owner for shared facts." They've reinvented, by hand, the exact problem they
tell you to automate away.

  ▲ **ewistrand (OP)** 3 hours ago [−]

  Caught the sharpest internal tension in the repo, congrats. The defense: prose
  reinforcement across standalone files is a different animal from a value copied
  out of code. You *want* each reference to read alone when followed in
  isolation, and you can't generate English restatements from a source of truth
  the way you can generate a CLI flag table. The change-set table is the
  acknowledgment that this duplication has a cost and someone has to pay it on
  every edit. It's not free; it's tracked. Whether "tracked manual duplication"
  is better than "one canonical statement + links" is a real judgment call and I
  won't pretend the table fully wins it.

    ▲ **wsxcde** 3 hours ago [−]

    "Not free, but tracked" is the right framing and I'll accept it. The table
    existing at all puts this in the top decile of docs-about-docs. Most projects
    duplicate silently and let it rot.

▲ **userbinator** 4 hours ago [−]

Strip the AI framing and this is just... good information architecture from
1998. Audience-split docs, a routing index, one topic per file, don't repeat
yourself, generate derived content. We knew all of this. The novel part is
purely that the "reader" has a context window and a per-token cost, which turns
old soft guidelines ("keep it organized") into hard constraints ("exceed the
window and recall goes to zero"). Interesting that an aesthetic preference
becomes an engineering requirement once the reader is billed by the word.

  ▲ **g:**hn_pedant** 3 hours ago [−]

  This is the comment I'd keep if I could keep one. The contribution isn't new
  principles, it's that a hostile-but-honest reader (finite window, literal,
  charges by the token, won't infer what you didn't link) makes the *cost of
  ignoring the old principles* suddenly legible. That reframing is the value, and
  the repo mostly doesn't claim more than that.

---

## What the thread is actually saying (author's read)

The strongest, most-upvoted critiques, distilled — these are the real review:

1. **Structure ≠ content (tptacek).** The layout helps only once the docs exist;
   the skill should be louder that it solves routing, not authoring.
2. **Unmeasured (zydeco).** Every efficiency claim is mechanism-plausible and
   evidence-free. No benchmark, and the honest answer is "distilled from daily
   use, not an eval."
3. **Recall vs. cost is not free (nostrademons).** On-demand loading trades token
   cost for retrieval misses; a confident edit made without the deep-dive that
   existed one hop away is the real failure mode. The "thick hub for small repos"
   mode is the honest boundary.
4. **Docs-for-agents may be a smell (jasode).** Invariants want to be types,
   gotchas want to be tests; what's left is decision history, justified only if
   "cheaper than commit archaeology" holds. *Answered in a later commit:* the new
   "What belongs in a doc" section makes executable enforcement rule #1 and
   reframes prose as compensating for the *agent's amnesia* (no memory between
   sessions), not the code's inadequacy. The residual worry the thread keeps: a
   findings doc is a cache, and a stale cache lies with more authority than a
   commit because it sits where the agent reads first.
5. **Internal tension: manual duplication (wsxcde).** The duplicated-rules
   change-set is hand-managed drift control in a project that preaches single-
   owner facts. Defensible (prose reinforcement ≠ code-derived copy) but it isn't
   free, it's tracked.
6. **Behavioral claims have a shelf life (majke).** "Agents `head`-preview nested
   files" is the empirical load-bearing assumption; if a model release fixes it,
   a workaround is baked into the method.

What the thread *praises*, and rightly: the **@-import footgun** call-out
(eager loading defeats the hub), the discipline of documenting **deliberate
non-decisions** (no link-checker, no example project), the **"No AI-isms"**
style guide as generally useful prose advice, and the **honesty of the
boundaries** (small repo → one file; no benchmark → say so).

The fair one-line verdict from the thread: *known information-architecture
principles, made into hard constraints by a reader that is billed by the token —
valuable for the routing rules and the rejected-options log, oversold wherever
it implies the efficiency win is free or measured.*
