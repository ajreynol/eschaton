# Competing approaches

Three bets on how to get a better SMT solver than the ones we have. **Proof-first
design is the current preference; none has been tested here.** Its existing
verified kernel in Logos and concrete next experiment make it the most promising
path to explore. They differ on what they keep from cvc5 and on who does the writing;
what already exists in public for each is
[`related-work.md`](related-work.md).

| | keeps from cvc5 | written by | the bet | dies if |
| --- | --- | --- | --- | --- |
| Proof-first design | the calculus, and parts of the internal proof checker | people | the proof should come first, and the build order is why cvc5's gaps exist | a proof-carrying rewriter costs too much |
| Automated maintenance | all of it | agents | the design is the asset; mechanize the upkeep and the build order stops mattering | upkeep does not outrun accumulation |
| Agent-built solver | nothing | agents | solvers are scarce because people are, so make architecture cheap to vary | the hard parts are exactly the parts that do not automate |

**Proof-first design and automated maintenance are the real disagreement.**
The former says cvc5's proof holes follow from proofs having been bolted onto a
working solver, so the order has to be inverted; the latter says the order is a
rate problem and agents change the rate.
Both cannot be the interesting question.

**Building a new solver with agents is arguing about something else.** It shares
automated maintenance's method and proof-first design's willingness to start
over, and it is the only one whose bet is about *production cost* rather than
correctness — which is also its weakness: nothing in it
produces a reason to believe the output. It is also the only one somebody has
already done. LLM2SMT built an agent-written QF_UF solver competitive on
SMT-LIB, so the question there is scale and coverage rather than possibility,
and proof emission is where that project struggled most.

## What proof-first design would keep

Proof-first design is **not** "rip the spine out of cvc5 and build around it",
though it is closer to that than the other two. It keeps the **proof calculus** — the
`Cpc.eo` signature cvc5 already emits against — **parts of the internal proof
checker**, and dokimasia's measurements of where cvc5's proof coverage has
holes. Its kernel is [Logos](https://github.com/ajreynol/logos), a verified
checker in Lean that already exists; what this approach would build is the
producer. The search does not come across.

[dokimasia](https://github.com/ajreynol/dokimasia) is the authority on what that
checker is, and measures it rather than describing it: `ProofChecker` plus the
thirteen registered theory rule checkers under `--check-proofs`, a compile
closure of 179 files and 41,446 lines — about 8% of cvc5's `src/` by line count.
Which parts to keep is not decided.

The spine transplant proper — keep cvc5's core engine as a working artifact and
rebuild the architecture around it — is a fourth bet, unclaimed here rather than
rejected.

## How any of this gets decided

Each bet has a cheap experiment that could kill it, and none has been run: a
proof-carrying rewriter for one theory; one agent-driven refactor of one cvc5
subsystem measured against the gaps it was meant to close; or a new theory
solver that passes somebody else's benchmark set. The preference for proof-first
design remains provisional until those experiments provide evidence to judge
the approaches.
