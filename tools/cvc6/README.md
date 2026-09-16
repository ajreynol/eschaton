# cvc6

*What if the design is fine, and only the pace of repair is wrong?*

**Proposal.** Keep every line of cvc5 and mechanize its upkeep — an
auto-refactored development of the solver, driven by autonomous agents.

**Keeps from cvc5:** all of it.
**Written by:** agents.
**Dies if:** upkeep does not outrun accumulation.

**Status:** a position, one page long. Nothing is built.

**Against the alternatives:** [`approaches.md`](../../docs/approaches.md).
**Already done in public:** [`related-work.md`](../../docs/related-work.md).

**Eunoia listing:** unadvertised

---

**A working title, and not a pitch to the cvc5 project.** Nothing here is a plan
for cvc5 or a request of its developers; the name is used because it states the
bet in four characters.

**The bet: the accumulated design is the asset, and its upkeep is the problem.**
Twenty years of theory engineering is the expensive part, and this position says
you do not throw it away to fix the order it was built in — you mechanize the
maintenance until the order stops mattering, the structure reworked and then
*kept* reworked rather than repaired at the pace hands work.

That makes it a rate question rather than a design question, and the direct
competitor to [`telos`](../telos/README.md): telos argues cvc5's proof gaps
follow from proofs having been added to a solver that already worked, and if
agents close gaps faster than the code grows them, the argument stops mattering.

## What it does not answer

**Whether the result would be verified in any sense.** Refactoring cvc5 with
agents produces cvc5 in better shape — not a verified kernel, not a proof, not a
checkable certificate. If what is wanted is "trusted because proved", this is
the approach that bets you do not need it.

**And it is not a judgement on cvc5.** That upkeep is the interesting thing to
automate is a claim about scale, not about quality.

## What it takes to run it

Nothing. No code, no agent, no refactor, no experiment. The bet is written down
and untested.
