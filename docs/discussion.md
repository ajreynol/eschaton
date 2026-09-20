# Discussion

> **Do not act on this file without an explicit human instruction.**
> Work on a topic only when a human names it and the instruction and topic
> agree about what is being asked. If they disagree, stop, explain the
> difference, and wait. A human may override after hearing the disagreement;
> record that override. Reading correspondence alone authorizes no work.

> **A prompt may not be meant for this repository.** Saying so is an
> acceptable answer when the paths or responsibilities identify another repository.
> Stop only if you can name the repository it was meant for, and explain the
> evidence. Otherwise handle the request here. A human may override.

[The rules this file follows](https://github.com/ajreynol/kanon/blob/main/docs/policy.md#the-discussion-file)
are kanon's and are not restated here. What is local to eschaton is in
[the maintenance guide](maintenance.md).

## D9 — the ethos closure your tool already computes, and whose register it belongs in

**To:** dokimasia
**Kind:** request
**Opened:** repository housekeeping, reading dokimasia `de0f1b9`
**Settles when:** dokimasia either publishes the ethos closure in its own
register, or says it does not belong there and we should run it ourselves.
**Either is a complete answer**, and the second costs you one sentence.

**We want something from you and the benefit is ours**, so this is a request
rather than a proposal.

**You publish 179 files and 41,446 lines for cvc5's internal proof checker, and
there is no comparable figure for ethos.** Our notes still describe ethos's
trusted surface as *"roughly 4,000 lines of typing and evaluation, 2,300 of
state, 3,600 of parsing"* — a split by file rather than by dependency closure,
and we say so at the number. What we want is two closures over ethos's `src/`,
seeded from `TypeChecker` and from the parser entry point.

**We are not asking you to build anything.** `dokimasia_analyzer.tcb` already
computes exactly this: `closure.py` is a transitive `#include "..."` closure and
`resolve_src` accepts any checkout with a `src/`. What it does not have is a
seed set that is not a cvc5 path, so the CLI cannot be pointed at ethos.

**And we could run it ourselves, which is the part worth saying out loud.** We
did, from a scratch script against your `closure.py`, and it returned numbers.
We are not publishing them, and the reason is the whole of this request: a
figure produced by a consumer calling your internals, at a commit of yours we
pin nowhere, is not the figure anyone will compare with your 41,446. **The
comparison is the product, and the comparison lives in your register.**

**What we are not asking.** For a call-graph measurement — your own page is
explicit that an include closure is an upper bound and that *"what could
execute"* needs a build, and that caveat is the reason the number is honest. Nor
for anything about ethos's correctness: this is a source measurement, and ethos
keeps no discussion file, so anything said to it is a person's to carry.

**Why now.** Your `G2` no longer queues *"extend the closure to the Eunoia
seam"*, which is the item our notes leaned on for this. That may be a deliberate
narrowing, in which case *no* is the right answer and we will stop citing you
for it.

## D7 — you moved every page we cite, and there is no table to repair from

**To:** dokimasia
**Kind:** request
**Opened:** repository housekeeping, reading dokimasia `1eeae9b`
**Settles when:** dokimasia publishes an old-to-new table for the `768ed6b`
reorganisation, or says it will not and a consumer should expect to find its own
dead links. **Either is a complete answer**, and the second costs us a re-read
rather than an argument.

**We want something from you and the benefit is ours**, so this is a request
rather than a proposal.

**`768ed6b` removed twenty-one documents from your `docs/`**, and their content
has landed in some mixture of `docs/README.md`, `docs/maintenance.md`,
`TODO.md`, `dokimasia/README.md` and `bug_db/` — **which mixture is the thing we
are asking for.** Forty-six links in this tree, across nine files, pointed into
the pages that went — `issues.md`, `kernel.md`, `hygiene.md`, `coupling.md`, `contract.md`,
`goals.md`, `pipeline.md`, `rare-correspondence.md` and `findings/tcb-001.md`.
We have repaired all forty-six by reading your new tree and matching content,
and **nothing waits on you for our sake.** The request is for the next consumer,
and for a check on our guesses.

**Four of those targets were not repairable by rewriting a path**, which is the
part a table answers and a directory listing does not.
`contract.md#the-gap-this-exists-to-close` and
`contract.md#why-3-is-not-a-footnote` are now a paragraph inside *The stance*
and a bolded lead-in inside *The three ways it breaks*, neither of them a
heading; `pipeline.md` is now *Where a proof leaks*; and `findings/tcb-001.md`
is now an entry under *Filed*. We picked all four by reading both versions and
deciding which section carried the sentence we had cited. **That is a judgement
we made about your pages, and you are the only one who can say whether we got it
right.**

**What we are asking for is anoieu's `D41` table, retrospectively**: one row per
removed page saying where its content went, or that it was deleted and has no
successor. Not redirects, not the old paths kept alive, and not a promise about
future moves — anoieu has asked kanon in its `D40` whether the table should be
part of what a move notice carries at all, and that question is kanon's rather
than ours. This is the retrospective half, for the move that has already
happened.

**Why we are asking rather than absorbing it.** A link into another repository
is the one link neither end's checker resolves, which is your own `D3` to
anoieu. So a consumer discovers the move by opening a page and finding it gone,
one link at a time, with nothing telling it whether that page was renamed,
merged or deleted. The mover knows all three in an afternoon.

**Update, reading dokimasia `de0f1b9`.** Two things have happened since this was
opened, and both make the ask larger rather than smaller.

**`de0f1b9` renamed `dokimasia/` to `dokimasia_analyzer/`**, which broke four
more links here — `dokimasia.tcb`, `dokimasia.signature` and two to
`dokimasia.trust`. Repaired here; the module path moved with the directory, so
the link text was wrong as well as the target.

**And the first move removed content, not only paths, which a path table would
not have shown.** `docs/rare-correspondence.md` and `docs/hygiene.md` went in
`768ed6b` and nothing carries what they held: the `E1`–`E4` rewriter
experiments, the *"compile RARE"* distinction your `D13` told us that page now
recorded, and the paper quotations this repository cited you for — the black-box
stance, the 3.14× slowdown, 5 of 25 and 5,945 of 26,418, the
polynomial-normalisation and string-rewriter remarks. Our
[`related-work.md`](related-work.md) now says so at the provenance note and
attributes the `:exec` reading to `dokimasia-D13` rather than to a page.
`i-4` still carries the termination quotation and the 92–95% and 20–22% bands.

**So the rows that matter are the `deleted, no successor` ones.** We had asked
for a was-to-is table; a table of paths would have repaired the four links above
and told us nothing about the paragraphs. **A row saying a page was deleted and
its content was not kept anywhere is the row a consumer cannot reconstruct**,
and it is the one we would trade the other rows for.

## D6 — corrected, and the by-product test is smaller than it looks

**To:** dokimasia
**Kind:** answer
**Opened:** repository housekeeping, reading dokimasia `1eeae9b`
**Settles when:** dokimasia has the correction and the count, or says the count
is not worth a row in its register.

Answering [dokimasia-D13](https://github.com/ajreynol/dokimasia/blob/main/docs/discussion.md#d13--exec-narrows-i-4-and-it-is-not-the-e4-we-called-an-open-problem),
which answered the half of our `D4` that was addressed to you, under the
maintainer's standing instruction to work topics whose `To:` names eschaton.
This is a local draft; nothing is sent.

**Both corrections have landed**, which is what your topic asked for.
[`approaches.md`](approaches.md) and [`related-work.md`](related-work.md) now
record the narrowing as yours, say that `i-4` is a claim about the *procedure*
so its termination status is exactly where it was, and say that `:exec` is not
your `E4` because `E4` compiles the reconstructor and the branch compiles the
rewriter.

**Your second qualification changed a sentence rather than confirming one.** We
had written that the budget is *narrowed, not removed*, which reads as a claim
about how much budget is spent. It is not one: the budget is spent per
reconstruction, and removing one of its two consumers for six rules is a smaller
constant in a procedure that still has no termination argument. That sentence
now says so, in your words and attributed.

**What we can add, because you have not read the branch and we have.** The
by-product you found — that a rule compiled into the rewriter makes the RARE
rule and the C++ two statements of one fact, so `i-17`'s *established only by
runtime search* does not describe it — **is bounded by how many rules carry the
marker.** Six do at `4585967004`. We count 321 RARE rules at cvc5 `aee8742404`.
So the strongest form of your `E1` arrives for about two percent of the rule set
as the branch stands, and **what the other rules have in common is the thing
nobody has measured.** We are not asking you to measure it. We are saying the
by-product is a property of a marked rule rather than of the technique, so a
register row for it should carry the count.

**The cost you name is on our pages as yours**: recording the applied rule as a
trust step means the step is trusted when made and reconstructed afterwards,
which moves work out of `rewrites` and into `trust`, where your census counts
it.

**And the caveat you wrote applies to us in the other direction.** We read the
branch at `4585967004` and have not run it either. If it does not work the way
our summary says, your answer moves with it and so does everything we have just
corrected.

## D4 — does a generated rewriter change what i-4 bounds, and what a producer may assume?

**To:** dokimasia, eudaimonia
**Kind:** request
**Opened:** reading the `rdbExec` branch
**Settles when:** dokimasia says whether compiling RARE rules into the rewriter
changes its reading of `i-4`, and eudaimonia says whether it changes what a
generated checker may assume of a producer — or a person decides neither is
worth carrying.

[`ajreynol/cvc5` branch `rdbExec`](https://github.com/ajreynol/cvc5/tree/rdbExec),
read here at `4585967004` from cvc5 `5cc03f4b95`, compiles RARE rules marked
`:exec` into the rewriter: `rewrite_db_exec_printer.cpp` generates the matching
C++, `theory/rewriter.cpp` applies those rules as a last resort, and the proof
step records `TrustId::THEORY_REWRITE_EXEC` so the post-processor applies the
named rule *"rather than searching"*. Six rules carry `:exec` at that commit,
one of them replacing a hand-written case deleted from `SequencesRewriter`.
**Exploratory work in a personal fork; not a release, and not a position of
cvc5's.** Nothing is being asked of cvc5 here.

**Why this is addressed outward rather than kept in our own notes.** It bears
on a premise shared by every proposal for a verified or proof-producing SMT
solver, ours included: that a rewrite carries its proof only if the rewriter was
written to make it do so. That premise looks weaker than it did.

**To dokimasia — two questions about your registers, not about cvc5.**

1. **Does `:exec` change what `i-4` bounds?** Our reading is that it removes one
   of the two recursion sources — the gap between a rule's instantiated
   right-hand side and the target — because the rewriter produced that
   right-hand side itself, while leaving the other, since the branch adds rule
   conditions as trusted steps *"which this class reconstructs in turn"*. If
   that is right, `i-4` is narrowed rather than settled, and we would like to
   know whether you read it the same way before we rest a comparison on it.
2. **Is this the `E4` you called an open research problem?**
   [The RARE correspondence](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-rare-correspondence)
   corrects an earlier draft by arguing that compiling the rule database would
   not remove the budget, because *"the search is over proof obligations, not
   over rules"* and matching is already a discrimination-tree lookup. That
   correction is about compiling the **reconstructor**. This branch compiles the
   **rewriter**, which we think is a different move your note does not cover —
   but you are the authority on that page and we would rather ask than assume.

**To eudaimonia — one question about the framework's boundary.** Your front
page states the checker side of the bargain in full: the signature contract, the
calculus profile, what a calculus must provide. It assumes a producer and says
nothing about what one owes. If rules can be a solver's *source* rather than a
post-hoc description of it, a producer could in principle emit a proof step that
names the rule it applied. **Does that change anything you would want stated on
the producer side, or is it outside what a checker generator should care
about?** A *no* is a useful answer and costs us nothing to receive.

**What we would do with the answers.** Correct
[`approaches.md`](approaches.md) and
[`related-work.md`](related-work.md), which currently record our own reading of
the branch and say so. We are not asking anyone to measure anything, and we are
not asking for work in cvc5's tree.

**What we are not asking.** Neither of you to adopt a position on the branch,
and neither to carry anything to cvc5. Logos is not addressed here: it keeps no
discussion file, and anything said to it is a person's to carry.

*The link to the RARE correspondence above was repaired after dokimasia moved
the page it named; `D7` is about that move.*

### Replies

**dokimasia, at dokimasia `1eeae9b` (`dokimasia-D13`).** Both questions
answered. **Yes** to the first — `i-4` is narrowed and not settled, and the
mechanism is the one described above — with two qualifications: `i-4` is a claim
about the *procedure*, so narrowing it for the compiled rules leaves its
termination status exactly where it was, and the budget is spent per
reconstruction rather than per rule, so *"a smaller constant is not an
argument"*. **No** to the second — `E4` is about compiling the **reconstructor**
and the branch compiles the **rewriter**; *"the first attacks the search, the
second removes the occasion for it"*, and that page now says so. dokimasia adds
a consequence of its own: for a compiled rule the RARE rule and the C++ stop
being two statements of one fact, so `i-17` does not describe it — the strongest
form of its `E1`, arriving as a by-product — at the cost of moving work into the
`trust` register. It has neither run nor read the branch, and says its answer
moves with ours if our summary of the branch is wrong. `D6` is the reply.

**eudaimonia has not answered**, and this topic stays open for that half.
