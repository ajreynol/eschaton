# hawkeye

*What would you build if writing a solver were cheap?*

**Proposal.** Build a new SMT solver from nothing, written mostly by autonomous
agents.

**Keeps from cvc5:** nothing.
**Written by:** agents.
**Dies if:** the hard parts are exactly the parts that do not automate.

**Status:** an intention, one page long. Nothing is built.

**Against the alternatives:** [`approaches.md`](../../docs/approaches.md).
**Already done in public:** [`related-work.md`](../../docs/related-work.md).

**Eunoia listing:** unadvertised

---

**The bet: the scarce resource is people, not ideas.** There are few SMT solvers
because each one costs somebody a decade, and that cost decides which
architectures get tried, how many, and how long a bad one survives before
anybody admits it. If writing a solver becomes cheap, the architecture becomes
the thing you vary rather than the thing you commit to.

The question is **how much of a solver can be built without a person writing
it** — not whether agents can write code, but whether they can carry the parts
that make solvers hard: the invariants nobody states, the performance work that
is all context, and the long tail of theory combination where being nearly right
is being wrong.

**The small version is already done.** LLM2SMT built an agent-written QF_UF
solver competitive on SMT-LIB, so the question is not whether but at what scale
and coverage — and proof emission is where that project needed the most human
help. Details in [`related-work.md`](../../docs/related-work.md).

Starting from nothing is what separates it from [`cvc6`](../cvc6/README.md):
same tooling, opposite premise about whether cvc5's code is an asset or a
constraint.

## What it does not answer

**Whether the result can be trusted.** A solver written quickly is not a solver
known to be correct, and an agent's confidence in its own code is worth nothing.
If the answer is to come with a certificate, that commitment lives in
[`telos`](../telos/README.md) and would have to be designed in from the start
rather than added afterwards — which is the exact mistake telos exists to point
at.

**Nor whether it would be fast.** Competitive SMT performance is the part
everybody underestimates.

## What it takes to run it

Nothing. No solver, no agent harness, no benchmark, no line of code.

## The name

Not Greek, and nothing to do with sharp eyes. The Hawkeye is the University of
Iowa's mascot, and Iowa is where this is being written: the name says where the
solver comes from and not one thing about what it does.

A sibling name is sitting right there should a competing approach ever want one:
`cyclone`, Iowa State's mascot. Nothing here holds it, and the rest of the joke
writes itself.
