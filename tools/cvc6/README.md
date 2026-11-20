# cvc6

*What if the design is fine, and only the language is wrong?*

**Proposal.** Port cvc5 to Rust with agents, using its C++ as a general guide,
then refactor the port step by step until it can be verified: with Verus, or by
translating it to Lean with Charon and Aeneas and proving it against Logos's
definitions.

**Keeps from cvc5:** the design and its algorithms, as a guide. Not the code.
**Written by:** agents, under human supervision.
**Dies if:** refactoring for verification costs the performance that justified
Rust, or the verified part never grows beyond a small kernel.

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
   answers emit CPC proofs that Ethos, pathos or Logos can check before any
   proof about the code exists.
2. **Refactor towards verification.** Agents restructure the port until it can
   be verified, starting where the specification is simplest: term
   representation, the rewriter, the SAT core. The risk is that verifiable code
   is slower or much larger than code that was fast.

## The analogy: pathos

[Pathos](https://github.com/daniel-larraz/pathos), read at `af0fe49`, is the
closest existing instance of this bet, on the checker side rather than the
solver. It reports the following, none of it re-run here:

- an AI model wrote it, milestone by milestone, with a second instance
  reviewing each one;
- it is a drop-in Rust replacement for Ethos and `ethos-eoc`, kept equal to
  them by differential testing on six platforms;
- on cvc5's proofs it takes about 44% of Ethos's time and much less memory;
- its CPC checker, compiled from the signature by Ethos's `rust-meta` stage,
  is written in the subset of Rust that Charon and Aeneas translate to Lean.
  Pilot proofs relate the five-rule CpcMini checker to Logos's definitions,
  but there is no theorem about the full checker yet.

So the first phase — an agent-written port that matches the original and runs
faster — has been done for one C++ proof tool. A route from that Rust to Lean
proofs has been started. Ethos is much smaller than cvc5 and has no search, so
this is an analogy, not evidence about a solver. Kanon registers pathos as a
candidate associate of the ecosystem; it is not part of this repository.

## Two routes to verification

- **Verus.** Specifications and proofs in Rust, discharged by an SMT solver.
  The proofs stay next to the code, but the argument ends in the SMT solver.
- **Charon and Aeneas into Lean.** The Rust is translated to Lean definitions,
  and the proofs are written in Lean against Logos's semantics, the same
  specification telos uses. The argument ends in Lean's kernel. The cost is
  keeping the code inside the Rust subset Aeneas accepts, which pathos reports
  to be possible for a generated checker, and to have made it faster.

The Aeneas route ties this proposal to telos's specification, and makes the
next ordering possible.

## Working backwards from telos

The port need not start from the C++. [`telos`](../telos/README.md) is already
a verified Lean port of cvc5's rewriter and preprocessor. A Rust implementation
could be derived from it, using the C++ as a guide to performance, and proved
equivalent to it through Aeneas. The Lean theorems would then hold for the
fast code. This is the order pathos's pilot uses: a verified Lean definition
first, and Rust related to it by translation.

That ordering changes the bet. cvc6 would no longer verify a solver directly;
it would make telos fast. It has its own risks. telos's data representations
would constrain the Rust, and the refinement proof grows with the code. It is
untested.

## Against telos

This is the direct competitor to [`telos`](../telos/README.md). Both port
cvc5 with agents under supervision, and both aim at a verified solver. They
disagree about where the guarantee lives:

- **telos** verifies the calculus and its checker, in Lean, and leaves search
  untrusted. It already has a verified rewriter and preprocessor.
- **cvc6** verifies the solver's own code, in Rust with Verus or in Lean
  through Aeneas, and needs no per-run certificate once that is done.

The [telos language note](../telos/docs/language.md#rust--verus--the-runner-up-and-the-escape-hatch)
gives the arguments against Verus for telos's purposes. Proving the metatheory of a
dependently typed calculus is a poor fit for an SMT-backed verifier, and
"the SMT solver said so" is a short argument a reader cannot check. On the Verus
route cvc6 must answer both. Its answer is that it verifies ordinary program
properties, not a calculus, and that the trusted SMT solver is a different one
from the solver being verified. The Aeneas route avoids the second objection.

The two need not be rivals. telos's specification — Logos's SMT-LIB semantics
and the CPC rules — is the obvious source of the specifications cvc6 needs, and
working backwards makes telos the specification outright.

## What it does not answer

**Whether Verus or Aeneas scales to an SMT solver.** Verus has been used for
verified systems code, and Aeneas for a generated proof checker in pathos's
pilot. Whether either can carry a solver's theory reasoning is unknown.
The verified SAT solvers of
[the guarantee table](../telos/docs/kernel-of-cvc5.md#the-kinds-of-guarantee-on-that-list)
show what verifying search has cost so far.

**And it is not a judgement on cvc5.** Choosing to port the design is a claim
that the design is worth keeping.

## What it takes to run it

Nothing here. No code, no agent, no port. The bet is written down and
untested; pathos is somebody else's project and an analogy.

## The name

`cvc6` is cvc5's version number advanced by one: the same solver, carried
forward into another language. It is neither Greek nor a description of the
work, which is **a departure from the shared policy's naming rule for a child
project**, taken because no Greek word says *this one, one release later*.
Changing it is the maintainer's decision, not this directory's.
