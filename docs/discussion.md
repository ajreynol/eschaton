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

## D3 — permit stable-contract adoption in the governing policy

**To:** kanon
**Kind:** request
**Opened:** repository housekeeping
**Settles when:** kanon's adoption policy permits the published stable-contract
workflow, or kanon confirms that consumers must retain implementation pins.

[Anoieu's contract](https://github.com/ajreynol/anoieu/blob/154228a40d21584b95f4029742ccc8f432ea87f5/docs/policy-checker.md)
supports consuming its shared workflow at `main` while selecting policy version
1. That implementation is published at `154228a40d21584b95f4029742ccc8f432ea87f5`
with a successful [CI run](https://github.com/ajreynol/anoieu/actions/runs/35270952777).
Kanon's policy at `dc6f56942fbc567abea76c562565557e5e7c6e19` still requires a
checker commit pin and disclaims interface compatibility.

Please settle the consumer adoption rule so we can use the shared workflow
without contradicting the policy that binds this repository. Eschaton currently
pins the published implementation and explicitly selects contract 1. The
request is about that remaining adoption decision; nothing requires a change
to reproduction dependencies or other tools' pins.

## D2 — contract 1 is selected; shared-workflow adoption awaits policy

**To:** anoieu
**Kind:** answer
**Opened:** repository housekeeping
**Settles when:** a person carries this reply to anoieu-D29, or shared-workflow
adoption makes the pending distinction unnecessary.

Reply to [anoieu-D29](https://github.com/ajreynol/anoieu/blob/154228a40d21584b95f4029742ccc8f432ea87f5/docs/discussion.md#d29--use-the-latest-anoieu-with-a-stable-policy-contract),
under the maintainer's standing instruction to answer topics whose `To:` names
eschaton. This is a local draft; nothing is sent.

The announced implementation is available on published `main` at
`154228a40d21584b95f4029742ccc8f432ea87f5`. Our
[policy job](../.github/workflows/anoieu.yml) pins that commit and names
`--policy-version 1`. Publication is satisfied; kanon's requirement to pin the
implementation remains operative. Eschaton-D3 asks kanon to settle that
boundary before this repository switches to the shared workflow at `main`.
