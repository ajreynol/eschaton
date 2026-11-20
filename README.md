# eschaton

A place to work out **what an SMT solver would look like if the proof came
first**. An SMT solver decides whether a logical formula has a solution, and a
great deal of software — verifiers, compilers, type checkers — believes whatever
it says. This is about the solvers that would deserve that.

It asks one question: **which approaches to a better-founded SMT solver are
worth trying, and what would each cost?**

*Status: research notes. No solver is built here.*

## Approaches for SMT solvers

| Approach | What we would build | Main question |
| --- | --- | --- |
| **Proof-first port** | A port of cvc5 to Lean, written by agents, with every step justified in cvc5's proof calculus and that justification verified once in Lean, on the verified [Logos](https://github.com/cvc5/logos) checker. Search stays untrusted. | Does the approach extend from rewriting and preprocessing to search and theory combination, at acceptable runtime cost? |
| **Verified Rust port** | A fast port of cvc5 to Rust, written by agents with the C++ as a general guide, then refactored until it can be verified — with [Verus](https://github.com/verus-lang/verus), or by translating it to Lean with [Charon](https://github.com/AeneasVerif/charon) and [Aeneas](https://github.com/AeneasVerif/aeneas) and proving it against Logos's definitions. | Can the port be brought into a verifiable shape without losing the performance that justified Rust? |
| **Agent-built solver** | A new SMT solver built from scratch, written mostly by autonomous agents. | Can agents handle theory reasoning, correctness and performance with no existing solver to follow? |

**The proof-first port is the current preference, and a promising one.** An
existing proof-producing solver gives an AI agent three things to work
against:

- **a specification for every step**, in cvc5's proof calculus, so the agent
  ports control flow and invents no rules;
- **a verified checker** for that calculus, Logos, so correctness rests on
  theorems proved once and does not depend on trusting the agent;
- **a reference implementation**, cvc5 itself, for differential testing of what
  the theorems do not cover.

The rules and the checker are the trusted parts, and other people maintain
them. The agent writes only untrusted code or code whose correctness Lean checks.
A verified Lean port of cvc5's rewriter and preprocessor, written by agents under
human supervision outside this repository, is the first evidence for this
route. It covers simplification, not a solver.

Logos's
[correctness statement](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/README.md#correctness)
concerns parsed assumptions under its own semantics. Its parser and the match
to the original problem are outside the theorem, and its
[SMT-LIB conformance limits](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/docs/smt-lib-conformance.md)
constrain the fragment that could be claimed.

**All three approaches now involve agents.** They differ in where the guarantee
lives. The proof-first port relies on certificates checked against a fixed
calculus. The Rust port relies on proofs about its own code. The agent-built
solver has neither until it adds one.

**The first two may converge.** [Pathos](https://github.com/daniel-larraz/pathos)
is the closest analogy for the checker side: an agent-written Rust
reimplementation of Ethos, kept equal to it by differential testing, and
reported to be faster. Its compiled CPC checker is written so that Charon and
Aeneas translate it to Lean, where pilot proofs relate it to Logos. The same
bridge suggests working backwards: take the verified Lean port as the
specification, and derive a fast Rust implementation that Aeneas relates to it.

The full comparison is in
[`docs/approaches.md`](docs/approaches.md), and existing public work is in
[`docs/related-work.md`](docs/related-work.md).

## The question it does not answer

**Whether any of them yields a solver.** No solver exists. The rewriter and
preprocessor above are verified, but not by anything in this repository, and
the comparisons below have not been run.

**And nothing about whether an existing solver is correct.** It makes no claim
about cvc5, asks nothing of cvc5, and should not be quoted as though it did.
These approaches are not a pitch to its developers.

## Three approaches to rewriter maintenance

A rewriter simplifies terms, and a proof-producing solver must justify those
changes. These shared terms name three ways to maintain rewrites and their
proofs. They name techniques, not projects, and any approach above could use
them.

| Approach | What a maintainer edits | How the proof follows | Main question |
| --- | --- | --- | --- |
| **Generated rewriter** | Declarative proof rules from which executable rewrite code is generated. | The generated code records which rule it applied, so the proof producer can apply that rule directly. | How much rewriting can be expressed this way, and at what maintenance and runtime cost? |
| **Proof-producing rewriter** | Rewrite code that returns a proof with the rewritten term. | The rewrite builds its equality certificate as it runs, and types can require it. | Can this keep manual work per rule and runtime cost low enough? |
| **Proof-reconstructing rewriter** | Handwritten rewrite code and a separate collection of proof rules. | After rewriting, a reconstructor searches for a proof of the result. This is cvc5's current approach. | Can reconstruction keep up with changes and find a proof within its budget? |

**A proof-reconstructing rewriter is very hard to verify statically.** A
guarantee that every rewrite gets a proof must connect separately maintained
rewrite code, proof rules and bounded search. Checking a proof once it is found
does not show that reconstruction always finds one.

**The techniques combine.** The verified Lean rewriter combines the first two:
its rule table is generated from the proof calculus, and every result carries
its certificate. The
[`rdbExec` branch](https://github.com/ajreynol/cvc5/tree/rdbExec) of a personal
cvc5 fork, read at `4585967004`, combines the first and third: six rules marked
`:exec` are compiled into the rewriter, alongside handwritten rewriting and
reconstruction. It is exploratory work, not a cvc5 release or position.

**Next experiment:** compare these on one theory, measuring manual work per
rule, coverage, and the costs of building and checking certificates. Use
`rdbExec` as the baseline for generation inside an existing solver. The
[comparison](docs/approaches.md#rewriter-maintenance) develops the tradeoffs.

## Reading and checking it

The notes need no build. The [documentation index](docs/README.md) lists them,
and the [maintenance guide](docs/maintenance.md) covers the repository checks.
Proof-production gaps in cvc5 are measured in
[Dokimasia](https://github.com/ajreynol/dokimasia), not here.

## The name

ἔσχατον, *the last thing* — the word eschatology is built from, so this
repository is strictly named "the end of times", which is a lot to ask of a
folder of reading notes. It should mean the **beginning** of times: the subject
is new solvers. The word does one honest day's work besides — the last thing is
the finished proof, and the design question is asked backwards from it.

The ecosystem's name register is
[kanon's glossary](https://github.com/ajreynol/kanon/blob/main/docs/glossary.md#eschaton).

## How this repository is maintained

This repository is part of the **Eunoia ecosystem** and follows its shared
[repository policy](https://github.com/ajreynol/kanon/blob/main/docs/policy.md).

**Human maintainers:** [the current list in policy.md](https://github.com/ajreynol/kanon/blob/main/docs/policy.md#human-maintainers).

**Written by AI agents, under light human supervision.** A human directs the
work, reads what is published and decides what is filed. That supervision does
not vet the internal design or establish the correctness of the research claims.
Nothing reaches another project's issue tracker without human review, under the
shared [reporting policy](https://github.com/ajreynol/anoieu/blob/main/bug_db/reporting-policy.md).
