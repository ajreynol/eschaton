# eschaton

A place to work out **what an SMT solver would look like if the proof came
first**. An SMT solver decides whether a logical formula has a solution, and a
great deal of software — verifiers, compilers, type checkers — believes whatever
it says. This is about the solvers that would deserve that.

It asks one question: **which approaches to a better-founded SMT solver are
worth trying, and what would each cost?**

## Approaches under consideration

**telos is currently the most promising path.** It can build on the verified
[Logos proof checker](tools/telos/docs/logos.md), leaving the proof-producing
solver as the main work. This is a provisional research preference; none of
these approaches has been implemented or tested here.

| Approach | What we would build | Main question |
| --- | --- | --- |
| **[telos](tools/telos/README.md)** | A new solver designed around proofs from the start, reusing cvc5's proof calculus and parts of its checker, with Logos as the verified kernel and new, untrusted search. | Can every rewrite carry its proof at an acceptable authoring and runtime cost? |
| **[cvc6](tools/cvc6/README.md)** | An agent-driven evolution of cvc5: keep the existing solver and automate its refactoring and upkeep. | Can automated maintenance close gaps faster than they accumulate? |
| **[hawkeye](tools/hawkeye/README.md)** | A new SMT solver built from scratch, written mostly by autonomous agents. | Can agents handle theory reasoning, correctness, and performance at useful scale? |

**Preferred next step:** telos's [T2 experiment](tools/telos/TODO.md#t2--a-proof-carrying-rewriter-for-one-theory)
— prototype a proof-carrying rewriter for one theory and measure the manual
work per rule and runtime overhead. Its outcome determines whether to proceed
with the solver design.

The full comparison and tradeoffs are in
[`docs/approaches.md`](docs/approaches.md), telos's proposed guarantees are in
[`tools/telos/docs/design.md`](tools/telos/docs/design.md), and existing public
work is in [`docs/related-work.md`](docs/related-work.md).

*Status: nothing is built. This repository contains prose and no program.*

## The question it does not answer

**Whether any of them works.** There is no solver here, nothing is verified by
anything, and no claim on these pages has been tested by running it. A design
that survives every argument here has been argued for, not demonstrated.

**And nothing about whether an existing solver is correct.** In particular it
makes no claim about cvc5, asks nothing of cvc5, and should not be quoted as
though it did — including the directory called `cvc6`, which is a working title
for a bet and not a pitch to anybody.

## What it takes to run it

Nothing, because nothing runs: no code, no build, no test suite, no command, no
dependency. `git clone` is the whole setup and reading is the whole interface.

One directory per bet. [`tools/telos/`](tools/telos/README.md) is the only one
with any depth — research notes carried over from the child project of the same
name in [`dokimasia`](https://github.com/ajreynol/dokimasia).
[`tools/cvc6/`](tools/cvc6/README.md) and
[`tools/hawkeye/`](tools/hawkeye/README.md) are a page each.

## The name

ἔσχατον, *the last thing* — the word eschatology is built from, so this
repository is strictly named "the end of times", which is a lot to ask of a
folder of reading notes.

It should mean the **beginning** of times: nothing here is ending, the subject
is new solvers. The word does one honest day's work besides — the last thing is
the finished proof, and the design question is asked backwards from it.
