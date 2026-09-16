# eschaton

A place to work out **what an SMT solver would look like if the proof came
first**. An SMT solver decides whether a logical formula has a solution, and a
great deal of software — verifiers, compilers, type checkers — believes whatever
it says. This is about the solvers that would deserve that.

It asks one question: **which approaches to a better-founded SMT solver are
worth trying, and what would each cost?** Three bets are written down and set
against each other in [`docs/approaches.md`](docs/approaches.md); what already
exists in public is in [`docs/related-work.md`](docs/related-work.md). None has
been tested and nothing here ranks them.

The bet with the most behind it takes *statically verified* in one sense, fixed
in [`tools/telos/docs/design.md`](tools/telos/docs/design.md):

> a solver whose **kernel** is verified, whose **completeness** is a type, and
> whose **search** is untrusted and free to be as clever and as ugly as it needs
> to be.

The other two dispute that verification is the thing to buy at all, which is the
disagreement `approaches.md` exists to hold open.

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
