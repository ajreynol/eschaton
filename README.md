# eschaton

A place to work out **what an SMT solver would look like if the proof came
first**. An SMT solver decides whether a logical formula has a solution, and a
great deal of software — verifiers, compilers, type checkers — believes whatever
it says. This is about the solvers that would deserve that.

It asks one question: **which approaches to a better-founded SMT solver are
worth trying, and what would each cost?**

## Approaches for SMT solvers

**A proof-first solver is currently the most promising path.** It can build on
the verified [Logos proof checker](https://github.com/cvc5/logos), leaving
the proof-producing solver as the main work. Logos's
[correctness statement](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/README.md#correctness)
concerns parsed assumptions under its own semantics; its parser and the match
to the original problem remain outside the theorem. Its
[SMT-LIB conformance limits](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/docs/smt-lib-conformance.md)
also constrain the fragment we could claim. This is a provisional research
preference; none of these approaches has been implemented or tested here.

| Approach | What we would build | Main question |
| --- | --- | --- |
| **Proof-first design** | A new solver designed around proofs from the start, reusing cvc5's proof calculus and parts of its checker, with Logos as the verified kernel and new, untrusted search. | Can every rewrite carry its proof at an acceptable authoring and runtime cost? |
| **Automated maintenance** | An agent-driven evolution of cvc5: keep the existing solver and automate its refactoring and upkeep. | Can automated maintenance close gaps faster than they accumulate? |
| **Agent-built solver** | A new SMT solver built from scratch, written mostly by autonomous agents. | Can agents handle theory reasoning, correctness, and performance at useful scale? |

The [rewriter comparison](#three-approaches-to-rewriter-maintenance) below is
the first test of the proof-first proposal.
Choosing how to maintain rewrites is separate from choosing whether to build a
new solver or who writes it; any of these broader approaches could use these
rewriter techniques or a hybrid.

The full comparison and tradeoffs are in
[`docs/approaches.md`](docs/approaches.md), and existing public work is in
[`docs/related-work.md`](docs/related-work.md).

*Status: no solver is built. This repository contains research notes and
repository checks.*

There is no paper planned yet: these are research notes without a result.

## The question it does not answer

**Whether any of them works.** There is no solver here, nothing is verified by
anything, and no claim on these pages has been tested by running it. A design
that survives every argument here has been argued for, not demonstrated.

**And nothing about whether an existing solver is correct.** In particular it
makes no claim about cvc5, asks nothing of cvc5, and should not be quoted as
though it did. These approaches are not a pitch to its developers.

## What it takes to run it

Reading the research notes requires no build or dependencies. There is no solver
to run yet.

The [Anoieu policy workflow](.github/workflows/anoieu.yml) checks repository
conventions on every push and pull request, using a pinned checker commit.
The [maintenance guide](docs/maintenance.md) explains how to run it locally;
the [documentation index](docs/README.md) lists the rest of the written work.

## The name

ἔσχατον, *the last thing* — the word eschatology is built from, so this
repository is strictly named "the end of times", which is a lot to ask of a
folder of reading notes.

It should mean the **beginning** of times: nothing here is ending, the subject
is new solvers. The word does one honest day's work besides — the last thing is
the finished proof, and the design question is asked backwards from it.

The ecosystem's name register is
[kanon's glossary](https://github.com/ajreynol/kanon/blob/main/docs/glossary.md#eschaton),
which records this repository.

## Common questions

- **Where are cvc5's proof-production gaps measured?** [Dokimasia](https://github.com/ajreynol/dokimasia).
- **Where is the verified CPC checker?** [Logos](https://github.com/cvc5/logos), subject to its correctness statement above.
- **How do I check this repository?** Follow the [maintenance guide](docs/maintenance.md).

## Three approaches to rewriter maintenance

A rewriter simplifies terms, and a proof-producing solver must justify those
changes. We compare three ways to maintain rewrites and their proofs:
**generated**, **proof-producing**, and **proof-reconstructing rewriters**.
These are shared terms for the approaches.

| Approach | What a maintainer edits | How the proof follows | Main question |
| --- | --- | --- | --- |
| **Generated rewriter** | Declarative proof rules from which executable rewrite code is generated. | The generated code applies a rule and records its identity so the proof producer can apply it directly. | How much rewriting can be expressed this way, and at what maintenance and runtime cost? |
| **Proof-producing rewriter** | Rewrite code that returns a proof with the rewritten term. | The rewrite constructs its equality certificate as it runs. The proposal here uses types to require that certificate. | Can this keep manual work per rule and runtime cost low enough? |
| **Proof-reconstructing rewriter** | Handwritten rewrite code and a separate collection of proof rules. | After rewriting, a reconstructor searches for a proof of the result. This is cvc5's current approach. | Can reconstruction keep up with changes and find a proof within its budget? Very hard to verify statically. |

The maintenance tradeoff is where the connection between a rewrite and its
proof lives: in generated code, in the rewrite's return value, or in a later
search. These approaches can be combined within one solver.

**A proof-reconstructing rewriter is very hard to verify statically.** A
guarantee that every rewrite gets a proof must connect separately maintained
rewrite code, proof rules and bounded search. Checking a proof once found does
not establish that reconstruction will always find one. Generated and
proof-producing rewriters make that connection more explicit, but their rules,
generators and proof interfaces still need justification.

**The `rdbExec` branch advocates a hybrid.** In
[`ajreynol/cvc5`](https://github.com/ajreynol/cvc5/tree/rdbExec), read at
`4585967004`, six RARE rules marked `:exec` generate rewrite code alongside the
handwritten theory rewriters and their proof reconstruction. The generated
rules run when the theory rewriter leaves a term unchanged. Proof production
applies the recorded rule directly where possible, while retaining
reconstruction for conditions and fallback
cases. This is exploratory work in a personal fork, not a release or a position
of cvc5's. Our proof-producing rewriter remains a proposal.

**Next experiment:** compare generated and proof-producing rewriters with the
proof-reconstructing baseline on one theory, measuring manual work per rule,
coverage, certificate construction and checking costs. Use the hybrid
`rdbExec` design as the practical baseline for generation inside an existing
solver. The [comparison](docs/approaches.md#three-approaches-to-rewriter-maintenance) develops
the tradeoffs; [related work](docs/related-work.md) records the evidence.

## How this repository is maintained

This repository is part of the **Eunoia ecosystem** and follows its shared
[repository policy](https://github.com/ajreynol/kanon/blob/main/docs/policy.md).

**Written by AI agents, under light human supervision.** A human directs the
work, reads what is published and decides what is filed; that supervision does
not vet the internal design or establish the correctness of the research claims.
Nothing reaches another project's issue tracker without human review, under the
shared [reporting policy](https://github.com/ajreynol/anoieu/blob/main/docs/reports/reporting-policy.md).
