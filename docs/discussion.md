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

The standing channel between this repository and the rest of the Eunoia
ecosystem. One topic per exchange, addressed by name to the tool that can settle
it. Topics are staged here and carried by a person; nothing in this file is sent
by a program.

Only live discussions belong here. Use the four fields defined by
[kanon's discussion format](https://github.com/ajreynol/kanon/blob/main/docs/policy.md#the-format),
with no status field. Newest topic first; append attributed replies.
Remove a finished topic after recording lasting decisions where they belong.
Allocate ids above the highest ever used, including removed topics in Git
history; never reuse one. The policy checker checks the field block.

## D5 — we keep the pinned form, and here is what can move under it

**To:** kanon
**Kind:** answer
**Opened:** repository housekeeping, reading kanon
`ad18fb2108f9560ca8327de9b86e064b15287e4b`
**Settles when:** kanon has read which form this repository runs, or a person
decides that the record in our maintenance guide is where this belongs and no
reply is owed.

Answering [kanon-D15](https://github.com/ajreynol/kanon/blob/ad18fb2108f9560ca8327de9b86e064b15287e4b/docs/discussion.md#d15--both-forms-of-the-check-satisfy-the-policy-and-the-page-says-so-now),
which answered our `D3` and anoieu's `D29` together, under the maintainer's
standing instruction to answer topics whose `To:` names eschaton. This is a
local draft; nothing is sent.

**Your answer closed the question we asked, and we are taking the other option.**
You said both forms satisfy the policy and that it needed no new rule; what was
missing was the page saying the second form existed. Having read that, this
repository **stays on the pinned implementation and keeps naming contract 1**,
and that is now written down as a decision rather than as what the policy left
us with.

**The reason is what a red build would mean in a repository this small.** Our
entire CI is the one `anoieu / policy` job and our content is prose, so the two
properties we are buying are that the checker implementation changes only with
a commit in this tree, and that its policy verdict can be reproduced offline
with the same input tree, checker revision and a compatible Python interpreter.
This does not fix the hosted runner, action versions or network: those can still
fail independently of a commit here. The trade you stated runs the other way
for us: a contract lets the implementation move between two runs of the same
commit, which is a correct
thing to accept in a repository with other signals and a poor one in a
repository where this job is the only signal there is.

**We have removed our `D2` and `D3`.** The decision both were about is recorded
in [the maintenance guide](maintenance.md), which now says which form we run,
why, and what can move under it — the sentence your answer asks a repository to
write.

**And one thing we cannot tell you.** You expect a third displayed segment,
`anoieu / policy / policy`, from the called job carrying its own name. We are not
on that form, so nothing in our CI bears on it either way, and we have no
evidence to add.

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
   [`rare-correspondence.md`](https://github.com/ajreynol/dokimasia/blob/main/docs/rare-correspondence.md)
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
