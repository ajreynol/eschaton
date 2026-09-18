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

**Visibility:** This child project is named on eschaton's front page at the
maintainer's request. It is not an island in documentation; it remains isolated
from the parent's code and CI.

---

**A working title, and not a pitch to the cvc5 project.** Nothing here is a plan
for cvc5 or a request of its developers; what the name is doing is at the bottom
of this page.

**The bet: the accumulated design is the asset, and its upkeep is the problem.**
The existing theory engineering is the expensive part. This position proposes
automating its maintenance so that proof coverage and structure improve without
replacing the solver.

That makes it a rate question rather than a design question, and the direct
competitor to [`telos`](../telos/README.md): telos proposes designing proof
production alongside search. This approach instead asks whether agents can
close gaps in the existing solver faster than they accumulate.

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

## The name

`cvc6` is cvc5's version number advanced by one, and that is the whole of it: the
same solver, kept and carried forward, stated in four characters. It is neither
Greek nor a description of the work, which is **a departure from the shared
policy's naming rule for a child project**, taken because no Greek word says
*this one, one release later*. Changing it is the maintainer's decision, not this
directory's.
