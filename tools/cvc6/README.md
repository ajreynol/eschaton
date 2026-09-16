# cvc6

**A working title for a bet, and not a proposal to anyone.** Nothing here is a
plan for cvc5, a request of its developers, or a claim that a successor is
wanted or needed. The name is used because it states the bet in four characters;
no one outside this repository has agreed to anything.

**The bet: the accumulated design is the asset, and its upkeep is the problem.**
An auto-refactored development of cvc5 driven by autonomous agents — the
structure reworked and then *kept* reworked mechanically, rather than by hand at
the pace hands work. Twenty years of theory engineering is the expensive part,
and this position says you do not throw it away to fix the order it was built
in; you mechanize the maintenance until the order stops mattering.

It asks one question: **can the holes be closed faster than they accumulate?**
That is a rate question and not a design question, which is what makes it the
direct competitor to [`telos`](../telos/README.md) — telos's whole argument is
that cvc5's proof gaps follow from proofs having been added to a solver that
already worked, and if agents close gaps faster than the code grows them, that
argument stops mattering.

## What it does not answer

**Whether the result would be verified in any sense.** Refactoring cvc5 with
agents does not produce a verified kernel, a proof, or a checkable certificate;
it produces cvc5, in better shape. If the answer wanted is "trusted because
proved", this is not the approach that gets there — it is the approach that bets
you do not need it.

**And it is not a judgement on cvc5's current state.** That the upkeep is the
interesting thing to automate is a claim about scale, not about quality.

## What it takes to run it

Nothing, because there is nothing. This directory is empty apart from this file:
no code, no agent, no refactor, and no experiment has been run. The bet is
written down and untested.

*Status: a position, one page long.*
