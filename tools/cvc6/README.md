# cvc6

*What if the design is fine, and only the language is wrong?*

**Proposal.** Port cvc5 to Rust with agents, using its C++ as a general guide,
then refactor the port step by step until Verus can verify it.

**Keeps from cvc5:** the design and its algorithms, as a guide. Not the code.
**Written by:** agents, under human supervision.
**Dies if:** refactoring for Verus costs the performance that justified Rust,
or the verified part never grows beyond a small kernel.

**Status:** a position, one page long. Nothing is built.

**Against the alternatives:** [`approaches.md`](../../docs/approaches.md).
**Already done in public:** [`related-work.md`](../../docs/related-work.md).

**Footing:** `unadvertised-child` — unadvertised at the maintainer's request:
these are speculative research notes, omitted from default ecosystem status
audits. Eschaton's front page does not name or link this child project; it
remains isolated from the parent's code and CI.

---

**A working title, and not a pitch to the cvc5 project.** Nothing here is a plan
for cvc5 or a request of its developers.

## The bet

**cvc5's design is the asset; C++ is what stands between it and verification.**
The expensive part of cvc5 is its accumulated theory engineering. A Rust port
keeps that engineering and gains memory safety and an ownership discipline that
Verus can reason about. Verification then proceeds by refactoring, one
component at a time.

**The C++ is a guide, not a specification.** A line-by-line translation would
carry over cvc5's shared mutable state, global node manager and pervasive
aliasing, which are what make it hard to verify. The port should keep cvc5's
algorithms and its observable behaviour, checked by differential testing
against cvc5, and restructure freely wherever Rust or Verus calls for it.

**Two phases, with different risks:**

1. **Port.** Agents write a Rust solver that matches cvc5 on benchmarks. The
   risk is the usual one for agent-written code: confidence without proof. The
   mitigation is to keep cvc5's proof production, so that the port's `unsat`
   answers emit CPC proofs that Ethos and Logos can check before any Verus proof
   exists.
2. **Refactor towards Verus.** Agents restructure the port until Verus can
   verify properties of it, starting where the specification is simplest:
   term representation, the rewriter, the SAT core. The risk is that code Verus
   accepts is slower or much larger than code that was fast.

## Against telos

This is the direct competitor to [`telos`](../telos/README.md). Both port
cvc5 with agents under supervision, and both aim at a verified solver. They
disagree about where the guarantee lives:

- **telos** verifies the calculus and its checker, in Lean, and leaves search
  untrusted. It already has a verified rewriter and preprocessor.
- **cvc6** verifies the solver's own code, in Rust with Verus, and needs no
  per-run certificate once that is done.

The [telos language note](../telos/docs/language.md#rust--verus--the-runner-up-and-the-escape-hatch)
gives the arguments against Verus for telos's purposes. Proving the metatheory of a
dependently typed calculus is a poor fit for an SMT-backed verifier, and
"the SMT solver said so" is a short argument a reader cannot check. cvc6 must
answer both. Its answer is that it verifies ordinary program properties, not
a calculus, and that the trusted SMT solver is a different one from the solver
being verified.

The two need not be rivals. telos's specification — Logos's SMT-LIB semantics
and the CPC rules — is the obvious source of the specifications cvc6's Verus
proofs would need.

## What it does not answer

**Whether Verus scales to an SMT solver.** It has been used for verified
systems code; whether it can carry a solver's theory reasoning is unknown.
The verified SAT solvers of
[the guarantee table](../telos/docs/kernel-of-cvc5.md#the-kinds-of-guarantee-on-that-list)
show what verifying search has cost so far.

**And it is not a judgement on cvc5.** Choosing to port the design is a claim
that the design is worth keeping.

## What it takes to run it

Nothing. No code, no agent, no port. The bet is written down and untested.

## The name

`cvc6` is cvc5's version number advanced by one: the same solver, carried
forward into another language. It is neither Greek nor a description of the
work, which is **a departure from the shared policy's naming rule for a child
project**, taken because no Greek word says *this one, one release later*.
Changing it is the maintainer's decision, not this directory's.
