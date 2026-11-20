# telos

*What would you build if you started from the answer?*

**Proposal.** Port cvc5 to Lean with agents, justify every step in cvc5's
proof calculus, and verify that justification once, on a kernel that is already
verified. Search stays untrusted.

**Keeps from cvc5:** the proof calculus, and the C++ as the behaviour to match.
[Dokimasia](https://github.com/ajreynol/dokimasia) is the authority on cvc5's
proof coverage.
**Written by:** agents, under human supervision —
[`ajreynol/telos`](https://github.com/ajreynol/telos) was written that way.
**Dies if:** the approach does not extend from simplification to search, or its
runtime cost is prohibitive.

**Status:** a verified rewriter and preprocessor exist, in their own
repository: [`ajreynol/telos`](https://github.com/ajreynol/telos), read at
`5661141`. No solver is built. The notes in this directory are the design
record that preceded it; [where the two differ](#where-the-implementation-departs-from-these-notes)
is below.

**Against the alternatives:** [`approaches.md`](../../docs/approaches.md).
**Already done in public:** [`related-work.md`](../../docs/related-work.md).

**Footing:** `unadvertised-child` — unadvertised at the maintainer's request:
these are speculative research notes, omitted from default ecosystem status
audits. Eschaton's front page does not name or link this child project; it
remains isolated from the parent's code and CI.

**Research notes.** Nothing here makes a claim about cvc5, asks anything of
cvc5, or should be quoted as though it did. This directory ships no code: the
implementation keeps its own documentation, trusted base and CI.

---

## Why this is promising

**A proof-producing solver is a strong basis for an AI agent to write a
verified one in Lean.** cvc5 gives the agent a specification for each step
(CPC's rules), a verified checker for that specification (Logos), and a
reference implementation to test against (cvc5 itself). The agent ports
control flow, which is untrusted, and the trusted parts are maintained
elsewhere. A wrong strategy can lose a rewrite or differ from cvc5, but cannot
return a non-equivalent term. Differential fuzzing against cvc5 measures what
the theorem does not.

This combines two bets the parent keeps separate.
[`hawkeye`](../hawkeye/README.md) asks whether agents can write a solver, but
has no reason to believe the result. telos supplies that reason.
[`cvc6`](../cvc6/README.md) also ports cvc5 with agents, but verifies the code
instead of the steps. The open question is whether the approach extends from
rewriting and preprocessing to search, theory combination and conflict
explanation, where porting is harder and runtime cost matters.

**telos could also be the specification for a fast port.** A Rust
implementation derived from telos and proved equivalent to it through Charon
and Aeneas would inherit telos's theorems. That is the order
[pathos](https://github.com/daniel-larraz/pathos)'s pilot follows on the
checker side, and [`cvc6`](../cvc6/README.md#working-backwards-from-telos)
develops it. It would answer telos's runtime question without giving up its
guarantee. It is untested.

## What was built

At `5661141`, according to its
[README](https://github.com/ajreynol/telos/blob/566114153c36bff8145224bf8831250c079a51f3/README.md),
[design](https://github.com/ajreynol/telos/blob/566114153c36bff8145224bf8831250c079a51f3/docs/design.md)
and [trusted base](https://github.com/ajreynol/telos/blob/566114153c36bff8145224bf8831250c079a51f3/TRUSTED.md),
which are the authority; nothing was re-run here:

- **Two theorems.** `Telos.rewrite_correct`: every rewrite of a well-typed
  term is equivalent to it. `Telos.preprocess_correct`: the preprocessed
  assertions are equivalent to the input. "Equivalent" means equal in every
  model under Logos's semantics.
- **Rewriter.** A port of cvc5's `Rewriter` and theory rewriters for Booleans,
  arithmetic, UF and `distinct`, bit-vectors, arrays, strings in part, finite
  sets, datatypes and tuples, and quantifiers as far as they are Boolean.
  Scope is cvc5's safe-mode fragment.
- **Preprocessor.** A port of passes from cvc5's `ProcessAssertions`,
  including rewriting, non-clausal simplification, static learning and theory
  preprocessing with purification skolems.
- **How steps are justified.** The baseline is a CPC step checked by Logos's
  verified checker, with one theorem covering every composition. For speed,
  some steps are instead *static* (a cheap side condition, proved once to
  imply that the checker would accept) or *semantic* (a condition proved once
  to imply equivalence). The rule table is generated from the CPC signature;
  telos writes no rules of its own.
- **Proofs for auditing.** The rewriter prints CPC proofs that Logos's checker
  has accepted, for Ethos to re-check. Proof output for preprocessing steps is a
  stated goal.
- **Agreement with cvc5** is tested by differential fuzzing per theory, not
  proved. The design document records the rates and the cvc5 revisions they
  were measured against.
- **Trusted base:** Logos's specification, the translation of telos's terms into
  Logos terms, and the definition of equivalence, plus Lean's kernel and
  compiler. No `sorry` or declared axiom, but 43 `native_decide` axioms enter
  through Logos's proofs, so the Lean compiler is trusted. The SMT-LIB front end
  and Logos's parser are outside the theorem.
- **Logos pin:** [`ajreynol/logos`](https://github.com/ajreynol/logos)
  `f2001ba0`, not the `cvc5/logos` `56c7b409` these notes measured.

**A by-product: porting finds proof holes.** telos's `test/cvc5-holes/`
records cvc5 rewrites whose proofs at `dsl-rewrite` granularity contain a
`trust` step, found while porting. It reports that new Logos rules cover all
but one, and links a cvc5 pull request that fills them. That is telos's report
about cvc5. Dokimasia, not this directory, is the register for such findings.

### Where the implementation departs from these notes

- **Scope.** The proposal was a solver. The implementation is a rewriter and
  preprocessor, which covers
  [T2](docs/TODO.md#t2--a-proof-producing-rewriter-for-one-theory) and part of
  the producer side. Its own
  [`solver-plan.md`](https://github.com/ajreynol/telos/blob/566114153c36bff8145224bf8831250c079a51f3/docs/solver-plan.md)
  keeps the broader solver plan.
- **Where the justification comes from.**
  [I3](docs/design.md#i3--rewrites-prove-themselves-as-they-fire) proposed
  rules written once in the host and elaborated into a rewrite and its proof.
  telos takes its rules from CPC instead, and generates only the matcher. Its
  cost per rule is no proof work.
- **Certificates are not the only mechanism.** The notes assume every
  successful answer carries a runtime certificate
  ([I1](docs/design.md#i1--the-answer-carries-its-certificate)). telos uses
  checked certificates where they are cheap, and Lean proofs about faster code
  where they are not. Proof production became an auditing goal rather than the
  means of verification.
- **What is not measured.** The comparison with the `rdbExec` hybrid has not
  been run, and runtime cost is a stated non-goal, although performance work
  has begun. [I4](docs/design.md#i4--safe-mode-is-a-type-not-a-list) concerns a
  solver's configuration and remains untested, but the port adopts cvc5's safe
  mode as its scope.

## The charter

**The question.** *If the proof came first, what would the solver look like?*

**The goals** are [`TODO.md`](docs/TODO.md)'s, ordered by how fast each could
kill the project rather than by how much work each represents.

**The wishue** — the outcome if this went unusually well, and not a commitment:
**telos succeeds when Logos says `correct` for the intended input in the agreed
fragment**, a target somebody else maintains the goalposts for. Verdicts and
unsupported inputs are recorded separately.

**Out of scope**, in full in [`TODO.md`](docs/TODO.md#not-doing): writing a
kernel (Logos is the kernel); verifying the search under the current design;
claiming anything about cvc5, Ethos or Logos, since a fact about any of them
belongs in that project's register; and presenting this proposal as an
implemented solver.

## The premise

Dokimasia measures cvc5's proof coverage from the outside. Its
[kernel wishue](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#a-kernel-you-can-argue-about)
aims to make it easier to *argue* which part of cvc5 has to be right. telos asks
the complementary question, which dokimasia cannot ask because it studies a
design that is already fixed: **what would a solver designed around its proofs
look like?**

The design notes answer five of dokimasia's findings at cvc5 `aee8742404` with
five inversions. They are hypotheses about a design; none is a claim that cvc5's
development order caused the gaps.

| dokimasia found | telos's answer | where |
| --- | --- | --- |
| a missing proof is not said out loud ([H6](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#proof-hygiene)) | the answer type carries the certificate | [I1](docs/design.md#i1--the-answer-carries-its-certificate) |
| the calculus is stated three times and can disagree ([R1](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#r1--emit-the-tables-cvc5-already-has)) | stated once; checker, printer and docs are functions of it | [I2](docs/design.md#i2--one-definition-of-the-calculus) |
| completeness depends on a search budget ([i-4](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)) | the rewriter returns its justification | [I3](docs/design.md#i3--rewrites-prove-themselves-as-they-fire) |
| safe mode is a hand-maintained list ([i-5](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)) | the feature set is a type parameter | [I4](docs/design.md#i4--safe-mode-is-a-type-not-a-list) |
| trust steps without a stated reason ([i-9](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)) | a hole must name itself to typecheck | [I1](docs/design.md#i1--the-answer-carries-its-certificate) |

**There is a competing answer, [`cvc6`](../cvc6/README.md):** port cvc5 to
Rust and verify the code with Verus, rather than verifying a calculus and
leaving search untrusted. Both are agent-written ports of cvc5.
[`approaches.md`](../../docs/approaches.md) compares them, with telos as the
current preference.

## The foothold: Logos

[Logos](https://github.com/cvc5/logos) is a verified proof checker for CPC in
Lean 4. Its calculus is compiled from the same `Cpc.eo` signature cvc5 emits
proofs against, and its theorem says that if it prints `correct`, the
assumptions its parser read are unsatisfiable under its model semantics. The
parser, the match to the original input, and compilation are outside the
theorem, and its
[conformance limits](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/docs/smt-lib-conformance.md)
constrain the fragment.

**An unjustified `trust` step is not a sound CPC derivation Logos can certify.**
That sentence is where telos and dokimasia meet.

Measurements, the guarantee in detail, and eight lessons are in
[`docs/logos.md`](docs/logos.md). **Read that first.**

## The language

**Lean 4**, because it can use Logos directly. The implementation has borne
this out for simplification. Rust remains an option for an untrusted search
core that emits certificates. The alternatives are in
[`docs/language.md`](docs/language.md).

## On the name

`telos` — τέλος — *end, completion, purpose*; Aristotle's final cause, the "that
for the sake of which". The design is asked backwards from the end state, which
is what the word is for.

## The notes

Design notes, kept as the record that preceded the implementation. The
measurements are of other people's trees, namely cvc5, Ethos and Logos, at the
revisions each page states.

- **[`docs/logos.md`](docs/logos.md) — the foothold: what Logos is, what it weighs, what its guarantee is, and eight lessons. Start here**
- [`docs/kernel-of-cvc5.md`](docs/kernel-of-cvc5.md) — what cvc5's two proof kernels are, and what each comparable tool's guarantee actually is
- [`docs/design.md`](docs/design.md) — the five inversions, the risks in each, and what the implementation tested
- [`docs/language.md`](docs/language.md) — the language decision record
- [`docs/TODO.md`](docs/TODO.md) — next steps, ordered, with what each produces

The same standard as the rest of the repository applies: **a number here comes
from something that ran**, and everything else is labelled as a design note.
