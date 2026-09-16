# hawkeye

A new SMT solver, written mostly by autonomous agents.

**The bet: the scarce resource is people, not ideas.** There are few SMT solvers
because each one costs somebody a decade, and that cost decides everything
downstream — which architectures get tried, how many, and how long a bad one
survives before anyone admits it. If writing a solver becomes cheap, the
architecture becomes the thing you vary rather than the thing you commit to.

It asks one question: **how much of a solver can be built without a person
writing it?** Not whether agents can write code — they can — but whether they
can carry the parts that make solvers hard: the invariants nobody states, the
performance work that is all context, and the long tail of theory combination
where being nearly right is being wrong.

It starts from nothing, which is what separates it from
[`cvc6`](../cvc6/README.md) — same tooling, opposite premise about whether
cvc5's code is an asset or a constraint.

## What it does not answer

**Whether the result can be trusted.** This is the one most people will assume,
and it is worth being blunt: a solver written quickly is not a solver known to
be correct, and an agent's confidence in its own code is worth nothing. Speed of
production says nothing about soundness. If the answer is to come with a
certificate, that commitment lives in [`telos`](../telos/README.md) and would
have to be designed in here from the start rather than added afterwards — which
is, unhelpfully, the exact mistake telos exists to point at.

**Nor whether it would be fast.** Competitive SMT performance is the part
everybody underestimates.

## What it takes to run it

Nothing, because there is nothing. No solver, no agent harness, no benchmark, no
line of code. This directory is one page describing an intention.

## The name

Not Greek, and nothing to do with sharp eyes. The Hawkeye is the University of
Iowa's mascot, and Iowa is where this is being written. That is the entire
etymology: the name says where the solver comes from and not one thing about
what it does.

The register would mark that down, since its convention asks for a word for
*what a tool does to its subject*. In fairness it is the opposite of the failure
the convention warns about — not an explanation that has to reach, but one that
does not reach at all — and the other half of the rule covers it: non-Greek is
allowed for a program rather than an account, and the practical test is whether
a name greps unambiguously, which this one does. A placeholder, and a cheerful
one.

A sibling name is sitting right there should some competing approach ever want
one: `cyclone`, Iowa State's mascot. Nothing in this repository holds it, and
the rest of the joke writes itself.

*Status: an intention, one page long.*
