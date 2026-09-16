# eschaton

A place to work out **what an SMT solver would look like if the proof came
first**. An SMT solver decides whether a logical formula has a solution, and a
great deal of software — verifiers, compilers, type checkers — believes whatever
it says. This repository is about the solvers that would deserve that, and about
which of the ways of building one are worth anybody's time.

It asks one question: **which approaches to a statically verified SMT solver are
worth trying, and what would each cost?** Comparing designs, pricing them
against prior art, and saying plainly which ones are dead is the work. The
phrase is used here in exactly one sense, fixed in
[`tools/telos/docs/design.md`](tools/telos/docs/design.md):

> a solver whose **kernel** is verified, whose **completeness** is a type, and
> whose **search** is untrusted and free to be as clever and as ugly as it needs
> to be.

Other readings of "verified solver" exist, and IsaSAT and versat are what they
cost. Those are on the record here as evidence, not as the road we are taking.

*Status: nothing is built. This repository contains prose and no program.*

## The question it does not answer

**Whether any of these approaches works.** That is the larger question, it is
the one most people will assume is being answered here, and it is not. There is
no solver in this repository, nothing is verified by anything, and no claim here
has been tested by running it. A design that survives every argument on these
pages has been argued for and not demonstrated, and the two are not close.

**It also says nothing about whether any existing solver is correct.** In
particular it makes no claim about cvc5, asks nothing of cvc5, and should not be
quoted as though it did. Where the notes here measure a real system, they are
measuring it to size a design problem — never to report a defect.

## What it takes to run it

Nothing, because nothing runs. There is no code, no build, no test suite, no
command, and no dependency to install. `git clone` is the whole setup and
reading is the whole interface.

What is here is [`tools/telos/`](tools/telos/README.md) — the research notes
this question has accumulated so far, carried over from the child project of the
same name in [`dokimasia`](https://github.com/ajreynol/dokimasia), where they
were a read-only tenant. They are a starting position rather than a conclusion,
and the copy here is not yet reconciled with the one there.

## The name

ἔσχατον, *the last thing* — the word eschatology is built from. So this
repository is, strictly, named "the end of times", which is quite a lot to ask
of a folder of reading notes.

It should mean the **beginning** of times. That is the joke and that is the
whole etymology: nothing here is ending, the subject is new solvers. The word
does one honest day's work besides — the last thing is the finished proof, and
the design question is asked backwards from it.

**Where it is a stretch.** Obviously. The ecosystem's register prefers a name
for *what a tool does to its subject*, and is mostly verbs of examination; this
is a noun for a destination, standing next to `telos` (τέλος, the end as a
goal), which means roughly the same thing in a quieter voice. The name stands —
this is a note for whoever reads it next, not an argument to reopen.

**The register does not have a row for this name.** Names in this ecosystem are
recorded in kanon's register, `tools/ynoia/names.md`, and as of kanon
`e65250ea` neither `eschaton` nor `telos` appears in it. So the etymology above
was written here first rather than taken from a line somebody else had already
written down, which is the wrong way round. The row is owed by kanon and is
filed as [`D1`](docs/discussion.md); it is not this repository's edit to make.
