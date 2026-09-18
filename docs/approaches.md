# Competing approaches

Three bets on how to get a better SMT solver than the ones we have. **Proof-first
design is the current preference; none has been tested here.** Its existing
verified checker in Logos and concrete next experiment make it the most promising
path to explore. They differ on what they keep from cvc5 and on who does the writing;
what already exists in public for each is
[`related-work.md`](related-work.md).

| | keeps from cvc5 | written by | the bet | dies if |
| --- | --- | --- | --- | --- |
| Proof-first design | the calculus, and parts of the internal proof checker | people | designing proof production with search may reduce reconstruction gaps | a proof-carrying rewriter costs too much, **or generating the existing rewriter from the rules closes the same gap more cheaply** |
| Automated maintenance | all of it | agents | the design is the asset; mechanize the upkeep and the build order stops mattering | upkeep does not outrun accumulation |
| Agent-built solver | nothing | agents | solvers are scarce because people are, so make architecture cheap to vary | the hard parts are exactly the parts that do not automate |

**Proof-first design and automated maintenance are the real disagreement.**
The former hypothesizes that designing proof production with search is cheaper
than reconstructing proofs afterwards; the latter asks whether improving the
existing implementation is cheaper still. The measurements do not establish
that development order causes the gaps, and the approaches could complement
each other.

**And there is now evidence in that disagreement, on the side of improving the
existing implementation.** The proof-first bet rests on the claim that a rewrite
can only carry its proof if the rewriter is written to do so from the start.
[`ajreynol/cvc5` branch `rdbExec`](https://github.com/ajreynol/cvc5/tree/rdbExec),
read at `4585967004` from cvc5 `5cc03f4b95`, is a third answer: **generate the
rewriter's matching code from the RARE rules**, so the rewrite *is* a rule
application and the proof step names the rule that fired instead of searching
for one. Exploratory work in a personal fork — six rules, no release, and no
position of cvc5's — but it closes part of the same gap **with no new solver,
no new language and no new kernel**, which is the cheapest bid on the table.
[`related-work.md`](related-work.md#where-a-rewrites-proof-comes-from--the-argument-all-three-bets-inherit)
describes it and the published stance it departs from; the mechanism and what
it does to the proof-first bet are in
[`design.md` I3](../tools/telos/docs/design.md#i3--rewrites-prove-themselves-as-they-fire).

**This narrows the question rather than settling it.** An `:exec` rewrite
removes one of the two things reconstruction recurses on — the gap between a
rule's instantiated right-hand side and the target — and leaves the other,
since rule conditions are still reconstructed through the same bounded search.
So the budget that
[i-4](https://github.com/ajreynol/dokimasia/blob/main/docs/issues.md) records is
narrowed, not removed. What nobody has measured is how far it goes: how many of
cvc5's RARE rules could carry `:exec` — 321 of them at cvc5 `aee8742404` — and
what the ones that cannot have in common. **That measurement, not a new
argument, is what would move this comparison.**

**Building a new solver with agents is arguing about something else.** It shares
automated maintenance's method and proof-first design's willingness to start
over, and it is the only one whose bet is about *production cost* rather than
correctness — which is also its weakness: nothing in it
produces a reason to believe the output. The LLM2SMT study reports
competitive QF_UF solving with much more limited certification; see the
[source summary](related-work.md#agent-built-solvers).

## What proof-first design would keep

Proof-first design is **not** "rip the spine out of cvc5 and build around it",
though it is closer to that than the other two. It keeps the **proof calculus** — the
`Cpc.eo` signature cvc5 already emits against — **parts of the internal proof
checker**, and dokimasia's measurements of where cvc5's proof coverage has
holes. Its kernel is [Logos](https://github.com/cvc5/logos), a verified
checker in Lean that already exists; what this approach would build is the
producer. The search does not come across.

[Dokimasia's measurement](https://github.com/ajreynol/dokimasia/blob/main/docs/kernel.md)
of cvc5 `aee8742404` identifies `ProofChecker` plus thirteen
registered theory rule checkers under `--check-proofs`: a compile closure of
179 files and 41,446 lines, about 8% of `src/`. This is a source dependency
measurement, not a minimal trusted-base proof. Which parts to keep is undecided.

Logos's [correctness statement](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/README.md#correctness)
concerns the assumptions its parser reads under its model
semantics. The parser, original-input correspondence and compiled execution
remain trust obligations. Its [conformance limits](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/docs/smt-lib-conformance.md)
mean that a `correct` verdict alone is not a general SMT-LIB guarantee.
The first Boolean rewriter experiment must check the input correspondence as
well as the verdict. `incomplete` measures unsupported translations; it does
not detect every semantic mismatch or prove completeness of search.

The spine transplant proper — keep cvc5's core engine as a working artifact and
rebuild the architecture around it — is a fourth bet, unclaimed here rather than
rejected.

## How any of this gets decided

Each bet has a cheap experiment that could kill it, and none has been run: a
proof-carrying rewriter for one theory; one agent-driven refactor of one cvc5
subsystem measured against the gaps it is meant to close; or a new theory
solver that passes somebody else's benchmark set. The preference for proof-first
design remains provisional until those experiments provide evidence to judge
the approaches.

**The first of those experiments now has a comparison to make, and it is not
the one it was designed against.** A proof-carrying rewriter for one theory was
meant to test the 2022 objection that instrumenting a rewriter costs too much
per rule. It must now also be measured against marking a RARE rule `:exec` and
regenerating the matcher, which is one token per rule. **An experiment that
beats the paper and loses to the branch has not settled anything in
proof-first design's favour**, and the design note says so where the claim
lives.
