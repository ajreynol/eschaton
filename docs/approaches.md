# Competing approaches

Three bets on how you would get a better SMT solver than the ones we have.
**None has been tested, nothing here ranks them, and each is one page old.**
This file exists so the differences are stated somewhere rather than rediscovered
in every conversation.

They differ on two things: **what they keep from cvc5**, and **who does the
writing**. What already exists in public for each is
[`related-work.md`](related-work.md).

| | keeps from cvc5 | written by | the bet | dies if |
| --- | --- | --- | --- | --- |
| [`telos`](../tools/telos/README.md) | the calculus, and parts of the internal proof checker | people | the proof should come first, and the build order is why cvc5's gaps exist | a proof-carrying rewriter costs too much |
| [`cvc6`](../tools/cvc6/README.md) | all of it | agents | the design is the asset; mechanize the upkeep and the build order stops mattering | upkeep does not outrun accumulation |
| [`hawkeye`](../tools/hawkeye/README.md) | nothing | agents | solvers are scarce because people are, so make architecture cheap to vary | the hard parts are exactly the parts that do not automate |

**telos and cvc6 are the real disagreement.** telos says cvc5's proof holes
follow from proofs having been bolted onto a working solver, so the order has to
be inverted; cvc6 says the order is a rate problem, and agents change the rate.
Both cannot be the interesting question. telos's own README names this competing
bet and declines to rank it, which is the right posture for now.

**hawkeye is not in that argument.** It shares cvc6's method and telos's
willingness to start over, and it is the only one of the three whose bet is
about *production cost* rather than about correctness. That is also its weakness:
nothing in it produces a reason to believe the output.

It is also the only one of the three that somebody has already done. LLM2SMT
built an agent-written QF_UF solver that is competitive on SMT-LIB, so hawkeye's
question is no longer *whether* — it is scale, theories, and the fact that the
proof-emission half is exactly where that project struggled most.

## One thing telos is not

telos is **not** "rip the spine out of cvc5 and build around it", though it is
closer to it than the other two. What it keeps from cvc5 is the **proof
calculus** — the `Cpc.eo` signature cvc5 already emits against — **parts of the
internal proof checker**, and dokimasia's measurements of where cvc5's proof
coverage has holes. Its kernel is [Logos](https://github.com/ajreynol/logos), a
verified checker in Lean that already exists; the part telos would build is the
producer. The search does not come across.

**On what that checker is, [dokimasia](https://github.com/ajreynol/dokimasia)
is the authority.** It measures it rather than describing it: `ProofChecker`
plus the thirteen registered theory rule checkers, run under `--check-proofs`,
with a compile closure of 179 files and 41,446 lines — about 8% of cvc5's `src/`
by line count. Which parts of it telos keeps is not yet decided, and saying "parts"
rather than a list is the honest state of that.

The spine-transplant position — keep cvc5's core engine as a working artifact
and rebuild the architecture around it — is a genuinely different fourth bet,
and **nothing in this repository holds it.** Worth noting that it is unclaimed
rather than rejected.

## How any of this gets decided

Not by argument on this page. Each bet has a cheap experiment that could kill it,
and none has been run: for telos, `T2`, a proof-carrying rewriter for one theory;
for cvc6, one agent-driven refactor of one cvc5 subsystem, measured against the
gaps it was supposed to close; for hawkeye, the first theory solver that passes
somebody else's benchmark set.

Until one of those returns, the ranking of the rows above is a matter of taste,
and this file should not pretend otherwise.
