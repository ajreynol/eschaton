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

**Footing:** `unadvertised-child` — unadvertised at the maintainer's request:
these are speculative research notes, omitted from default ecosystem status
audits. Eschaton's front page does not name or link this child project; it
remains isolated from the parent's code and CI.

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

**A small case study exists.** The LLM2SMT study reports competitive
QF_UF solving with limited proof certification. It does not establish that the
approach scales to a broader SMT solver. Details in [`related-work.md`](../../docs/related-work.md).

Starting from nothing is what separates it from [`telos`](../telos/README.md)
and [`cvc6`](../cvc6/README.md). Both of those are agent-written ports of cvc5;
this proposal follows no existing solver.

## What it does not answer

**Whether the result can be trusted.** A solver written quickly is not a solver
known to be correct, and an agent's confidence in its own code is worth nothing.
A certificate would have to be designed in from the start rather than added
afterwards.

**The ports show the missing piece.** An agent porting cvc5 into Lean against
CPC and Logos has a specification, a verified checker and a reference to test
against. [`ajreynol/telos`](https://github.com/ajreynol/telos), a verified Lean
rewriter and preprocessor written that way by agents under human supervision,
shows that agent authorship and verification need not conflict. That route is
[`telos`](../telos/README.md#why-this-is-promising)'s, not this one's: this
proposal keeps no calculus, no checker and no reference, so it would have to
supply all three.

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

**This is a departure, and it is named rather than argued for.** The shared
policy asks a child project for a Greek name that describes the work; this one is
neither. Changing it is the maintainer's decision, not this directory's.
