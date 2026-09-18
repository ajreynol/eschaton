# eschaton

A place to work out **what an SMT solver would look like if the proof came
first**. An SMT solver decides whether a logical formula has a solution, and a
great deal of software — verifiers, compilers, type checkers — believes whatever
it says. This is about the solvers that would deserve that.

It asks one question: **which approaches to a better-founded SMT solver are
worth trying, and what would each cost?**

## Approaches under consideration

**A proof-first solver is currently the most promising path.** It can build on
the verified [Logos proof checker](https://github.com/ajreynol/logos), leaving
the proof-producing solver as the main work. Logos's
[correctness statement](https://github.com/ajreynol/logos/blob/be4791204be5616df2bf6f42ea304b45b08d33e1/README.md#correctness)
concerns parsed assumptions under its own semantics; its parser and the match
to the original problem remain outside the theorem. Its
[SMT-LIB conformance limits](https://github.com/ajreynol/logos/blob/be4791204be5616df2bf6f42ea304b45b08d33e1/docs/smt-lib-conformance.md)
also constrain the fragment we could claim. This is a provisional research
preference; none of these approaches has been implemented or tested here.

| Approach | What we would build | Main question |
| --- | --- | --- |
| **Proof-first design** | A new solver designed around proofs from the start, reusing cvc5's proof calculus and parts of its checker, with Logos as the verified kernel and new, untrusted search. | Can every rewrite carry its proof at an acceptable authoring and runtime cost? |
| **Automated maintenance** | An agent-driven evolution of cvc5: keep the existing solver and automate its refactoring and upkeep. | Can automated maintenance close gaps faster than they accumulate? |
| **Agent-built solver** | A new SMT solver built from scratch, written mostly by autonomous agents. | Can agents handle theory reasoning, correctness, and performance at useful scale? |

**Preferred next step:** prototype a proof-carrying rewriter for one theory and
measure the manual work per rule and runtime overhead. Its outcome determines
whether to proceed with the solver design.

**There is a competing answer to the same question, and it needs no new
solver:** generate the existing rewriter's matching code from the rewrite rules,
so that a rewrite is a rule application and its proof names the rule rather than
searching for one. Exploratory work in a personal fork
([`ajreynol/cvc5` branch `rdbExec`](https://github.com/ajreynol/cvc5/tree/rdbExec),
read at `4585967004`) does this for six rules; it is not a release and not a
position of cvc5's. It narrows the gap the proof-first bet is aimed at without
replacing anything, so the experiment above has to be measured against it and
not only against the published objection it was designed to answer.
[`docs/related-work.md`](docs/related-work.md) has the literature and the branch;
[`docs/approaches.md`](docs/approaches.md) has what it does to the comparison.

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
- **Where is the verified CPC checker?** [Logos](https://github.com/ajreynol/logos), subject to its correctness statement above.
- **How do I check this repository?** Follow the [maintenance guide](docs/maintenance.md).

## How this repository is maintained

This repository is part of the **Eunoia ecosystem** and follows its shared
[repository policy](https://github.com/ajreynol/kanon/blob/main/docs/policy.md).

**Written by AI agents, under light human supervision.** A human directs the
work, reads what is published and decides what is filed; that supervision does
not vet the internal design or establish the correctness of the research claims.
Nothing reaches another project's issue tracker without human review, under the
shared [reporting policy](https://github.com/ajreynol/anoieu/blob/main/docs/reports/reporting-policy.md).
