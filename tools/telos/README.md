# telos

*What would you build if you started from the answer?*

**Proposal.** Keep cvc5's proof calculus and parts of its internal checker,
throw away the search, and design a solver in which the proof comes first — on a
kernel that is already verified.

**Keeps from cvc5:** the proof calculus and parts of the internal proof
checker, which [dokimasia](https://github.com/ajreynol/dokimasia) is the
authority on — not the search.
**Written by:** people.
**Dies if:** a proof-producing rewriter costs too much, or a hybrid using
generated rules closes the same gap more cheaply.

**Status:** a verified rewriter exists, in its own repository:
[`ajreynol/telos`](https://github.com/ajreynol/telos), read at `b1c20dc`.
No solver is built. The notes in this directory are the design record that
preceded it; [what was built](#what-was-built) says where the two differ.

**Against the alternatives:** [`approaches.md`](../../docs/approaches.md).
**Already done in public:** [`related-work.md`](../../docs/related-work.md).

**Footing:** `unadvertised-child` — unadvertised at the maintainer's request:
these are speculative research notes, omitted from default ecosystem status
audits. Eschaton's front page does not name or link this child project; it
remains isolated from the parent's code and CI.

**Research notes.** Nothing here makes a claim about cvc5, asks anything of
cvc5, or should be quoted as though it did.

**This directory ships no code.** The implementation lives in
[`ajreynol/telos`](https://github.com/ajreynol/telos), which keeps its own
documentation, trusted base and CI. Everything here is prose, and deleting it
would leave this repository exactly as functional as it is now.

## What was built

At `b1c20dc`, telos is a **port of cvc5's rewriter to Lean 4 with a
machine-checked proof that every rewrite preserves equivalence**, stated as
`Telos.rewrite_correct (t : Node) (h : t.WellTyped) : t.Equiv (rewrite t)`
under Logos's semantics. Its [README](https://github.com/ajreynol/telos/blob/b1c20dc87c79b7e378055b670f31684c14d19705/README.md),
[design](https://github.com/ajreynol/telos/blob/b1c20dc87c79b7e378055b670f31684c14d19705/docs/design.md)
and [trusted base](https://github.com/ajreynol/telos/blob/b1c20dc87c79b7e378055b670f31684c14d19705/TRUSTED.md)
are the authority; this section summarizes them and was not re-run here.

- **Every step is a CPC rule checked by Logos.** A matcher generated from the
  pinned Eunoia signature (88 of the signature's 610 rules at that revision: `bool-*`,
  `ite-*`, `eq-*` and arithmetic) finds an application, and the step runs Logos's verified
  checker on a short command list. Theory rewrites such as `aci_norm` and
  `arith_poly_norm` are computed by unverified code ported from cvc5 and
  validated by the rule. telos writes no rewrite rule of its own.
- **Choosing the rule is untrusted.** Hand-written strategies port the control
  flow of cvc5's theory rewriters. A wrong strategy can lose a rewrite or
  differ from cvc5, but not return a non-equivalent term. Agreement with cvc5 is
  tested by differential fuzzing, not proved.
- **Ported so far:** Booleans, arithmetic (`+`, `-`, `*`, `/`, `div`, `mod`,
  `abs`, `to_real`, `to_int`, `is_int`, comparisons), and equality, UF and
  quantifiers as far as they are Boolean. Bit-vectors and the other theories
  are not started.
- **Reported results:** on 6,000 fuzzed Boolean terms, about 94% identical to
  cvc5 and the rest differing by equality orientation; on 3,000 arithmetic
  terms, 83.5% identical and 92.7% up to orientation; no semantic
  disagreements; Ethos accepted 2,000 of 2,000 printed proofs.
- **Three cvc5 arithmetic rewrites have no CPC rule**, so telos cannot certify
  them: `(is_int (to_real x))` to `true`, removing `to_real` from both sides of
  `/`, and `(div_total (mod_total x c) c)` to `0`.
- **Trusted base:** the Logos/CPC definitions, the `Node`-to-Logos translation
  and the definition of `Node.Equiv`, plus Lean's kernel and compiler. No
  `sorry` or declared axiom, but 43 `native_decide` axioms enter through
  Logos's proofs, so the Lean compiler is trusted. The SMT-LIB front end and
  Logos's parser are outside the theorem.
- **Logos pin:** [`ajreynol/logos`](https://github.com/ajreynol/logos)
  `b1b49e7e`, not the `cvc5/logos` `56c7b409` these notes measured.

**Where it departs from these notes.**

- **Scope.** The proposal was a solver. The implementation is a rewriter, which
  covers [T2](docs/TODO.md#t2--a-proof-producing-rewriter-for-one-theory) and
  not the producer side. Its own
  [`solver-plan.md`](https://github.com/ajreynol/telos/blob/b1c20dc87c79b7e378055b670f31684c14d19705/docs/solver-plan.md)
  keeps the broader solver plan as the longer-term direction.
- **Where the certificate comes from.** [I3](docs/design.md#i3--rewrites-prove-themselves-as-they-fire)
  proposed rules written once in the host and elaborated into a rewrite and its
  justification. telos writes no rules. It takes them from CPC, generates only
  the matcher, and proves once that any composition of checker-accepted steps
  is an equivalence. The cost per rule is no proof work. Porting a strategy is
  ordinary programming.
- **What is not measured.** The comparison T2 asks for against the `rdbExec`
  hybrid has not been run. Runtime cost is an explicit non-goal at `b1c20dc`:
  there is no rewrite cache, and fuel replaces termination proofs.
  [I1](docs/design.md#i1--the-answer-carries-its-certificate) and
  [I4](docs/design.md#i4--safe-mode-is-a-type-not-a-list) concern a solver's
  answers and configuration, so they remain untested.

---

## The charter

**The question.** *If the proof came first, what would the solver look like?*
The parent compares this proposal with alternative ways to develop a solver.

The experiment explores a **proof-producing rewriter** and compares it with a
**generated rewriter** and cvc5's **proof-reconstructing rewriter**, using the
[parent README's shared terminology](../../README.md#three-approaches-to-rewriter-maintenance).
The `rdbExec` branch is a hybrid baseline: generated rules alongside handwritten
rewriting and proof reconstruction. These terms describe techniques usable by
any solver.

**The goals, in order** — they are [`TODO.md`](docs/TODO.md)'s `T1` to `T6`, ordered
by how fast each could kill the project rather than by how much work each
represents. `T2`, a proof-producing rewriter for one theory, is first because it
is the only one whose outcome could invalidate the design.

**The wishue** — the outcome if this went unusually well, and not a commitment.
It is already stated, and stated as a target somebody else maintains the
goalposts for: **telos succeeds when Logos says `correct` for the intended
input in the agreed fragment** — a fragment on
which a telos solver's output is accepted by an independently maintained
verified checker, with verdicts and unsupported inputs recorded separately.

**Out of scope**, in full in [`TODO.md`](docs/TODO.md)'s *Not doing*, and in
summary: writing a kernel (Logos is the kernel); writing a solver until `T2`
returns; verifying the search, ever, under the current design; claiming anything
about cvc5, ethos or logos, since a fact about any of them goes into that
project's register under its own name; and presenting this proposal as an
implemented solver.

## The premise

dokimasia measures cvc5's proof kernel from the outside, and the arc of that
measurement is stated in [dokimasia's kernel wishue](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#a-kernel-you-can-argue-about): make it
easier to *argue* which part of cvc5 has to be right, along five axes —
nameable, closed, small, local, mechanized — with mechanized named explicitly as
**the last axis, not the first**.

The design hypothesis is that constructing proofs alongside search could
reduce some reconstruction costs. Dokimasia's measurements motivate that
hypothesis; they do not prove that development order causes the gaps:
`ProofGenerator* pg = nullptr` is a default argument (cvc5 `aee8742404`, with
the call sites in [`design.md`](docs/design.md#i1--the-answer-carries-its-certificate)), the safe-mode
disable list is maintained by hand ([i-5](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)), and
completeness depends on a search budget ([i-4](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)) because the
rewriter does not construct these proofs as it rewrites.

telos asks the complementary question, and only the complementary question:

> **If the proof came first, what would the solver look like?**

Not a competitor to cvc5, not a replacement, not a proposal to anyone. A
research vehicle for the one experiment dokimasia cannot run, because dokimasia
is downstream of a design that is already fixed.

**There is a competing answer, and it is [`cvc6`](../cvc6/README.md).** The
opposite bet is that cvc5's *development procedure* is the thing to automate —
the existing design kept and its upkeep mechanized. Its question is whether
automated maintenance closes proof gaps more cheaply than a new design.
Both proposals are untested. [`approaches.md`](../../docs/approaches.md)
compares them, with telos
as the current, provisional preference.

## The foothold: Logos

**telos proposes to use [`cvc5/logos`](https://github.com/cvc5/logos), and
Logos is a moving target.** Everything below is downstream of that fact, so it
belongs before the design rather than after it.

Logos is a **verified proof checker for SMT in Lean 4**. Its soundness theorem
`correct___logos_check_proof` says: if the checker prints `correct` for a proof
file, the assumptions the parser read out of it are unsatisfiable — proved
against `Cpc/SmtModel.lean`, a standalone Lean formalization of SMT-LIB's model
semantics. Its calculus is **compiled from the same `Cpc.eo` signature cvc5
emits proofs against**, by `ethos-eoc`, and regenerated as CPC changes.

The measurements cover logos `56c7b409`; these are source sizes, not a complete
trusted-base measurement. The figures are copied from
[the measurement note](docs/logos.md#what-it-weighs); no automatic comparison
keeps the copy current:

| | ethos + the `cpc` signature | **Logos** |
| --- | --- | --- |
| source components compared | ≈**26,400 lines** — 13,862 C++, 12,530 Eunoia | **2,680 lines** of Lean specification |
| machine-checked soundness development | none established here | **691,928 lines** of proof |
| rules proved sound | — | **591 of 593**, no `sorry`/`admit`/`axiom` in 890 files |
| the calculus is | hand-written twice, in Eunoia and in C++ | generated from the signature, with drift caught in CI |
| outside the checker theorem | C++ checker and Eunoia signature | parser, input correspondence, specification adequacy and compilation |

The two CPC rules with no soundness proof are `beta-reduce` and **`trust`** —
cvc5's declared hole, the one
[`dokimasia_analyzer.trust`](https://github.com/ajreynol/dokimasia/tree/main/dokimasia_analyzer/trust/) censuses 75 ids of. It cannot have
one. Which states the relationship between these two projects in a sentence:

> **An unjustified `trust` step is not a sound CPC derivation Logos can certify.**

Dokimasia also reports static candidates and coverage risks; those are not a
one-to-one prediction of Logos verdicts.

At logos `56c7b409`, the theorem is about the assumptions the parser reads
under Logos's semantics.
Arrays, reals and uninterpreted sorts have documented model restrictions; these
do not necessarily cause `incomplete`. The parser, original-input match and
compiled execution remain trust obligations. Read the
[conformance limits](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/docs/smt-lib-conformance.md)
before choosing a fragment. A Boolean rewriter is the proposed first test.

Full description, measurements and eight things telos should learn from it are
in [`docs/logos.md`](docs/logos.md). **Read that first.**

Logos supplies an existing checker for the proposed producer. What
is unclaimed is the **producer** side — and that gives telos a definition of
success that is executable rather than rhetorical:

> **telos succeeds when Logos says `correct` for the intended input in the agreed fragment.**

## Derived from measurements, not from wishes

The design notes draw on dokimasia's findings at cvc5 `aee8742404`. The proposed answers remain hypotheses; none establishes a
property of an implementation here.

| dokimasia found | telos's answer | where |
| --- | --- | --- |
| a missing proof is not said out loud ([H6](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#proof-hygiene)) | the answer type carries the certificate; there is no proofless return | [`design.md`](docs/design.md#i1--the-answer-carries-its-certificate) |
| the calculus is stated three times and can disagree (`SIG`, [R1](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#r1--emit-the-tables-cvc5-already-has)) | stated once; checker, printer and docs are functions of it | [`design.md`](docs/design.md#i2--one-definition-of-the-calculus) |
| completeness depends on a search budget ([i-4](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)) | the rewriter returns its justification; there is no reconstruction to bound | [`design.md`](docs/design.md#i3--rewrites-prove-themselves-as-they-fire) |
| safe mode is a hand-maintained list ([i-5](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register), [R8](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#r8--safe-mode-as-a-build-time-property)) | the feature set is a type parameter; the unsafe configuration does not compile | [`design.md`](docs/design.md#i4--safe-mode-is-a-type-not-a-list) |
| 8 trust steps have no stated reason ([i-9](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)) | a hole must name itself to typecheck | [`design.md`](docs/design.md#i1--the-answer-carries-its-certificate) |

Five inversions, five findings. That is the whole design so far, and it is
enough to start.

## What cvc5's kernel actually is

Before defining a kernel you have to be able to point at the one that exists.
cvc5 has **two**, they are not the same size, and they do not trust the same
things. The measurements cover cvc5 `aee8742404` and ethos `b9188b86`:

| | internal — `--check-proofs` | external — `ethos` |
| --- | --- | --- |
| trusted C++ | **41,446 lines**, 179 files — 8.0% of `src/` | **13,862 lines**, standalone |
| trusted declarative | — | **12,530 lines** of Eunoia (the `cpc` signature) |
| the calculus is written as | `switch` bodies and LaTeX doc comments | 620 `declare-rule`, 340 constant declarations, 151 `define`, 248 `program` |
| shares code with the solver it checks | **yes** — 10 theory subsystems, the rewriter, `Env` | **no** |
| how it checks a rewrite | replays it — `MACRO_REWRITE`, registered trusted at pedantic level 4 | re-derives it from rules in the signature |

Read the last two rows together and the external checker is the real kernel: it
is the only one whose correctness is independent of the solver's. It is also
the smaller one, and its 13,862 lines are a *general-purpose* framework checker
rather than cvc5-specific code.

But the trusted total is not 13,862. It is 13,862 **plus 12,530 lines of
Eunoia**, of which **4,186 lines are `program` definitions** — side conditions,
which is to say a second implementation of polynomial normalisation, ACI
normalisation, bit-blasting and string reasoning, written in an unverified DSL
and evaluated by an unverified evaluator. `programs/Strings.eo` alone is 2,277
lines.

**That is the kernel to learn, and it is the right size to verify.** Not 41,446
lines of C++ entangled with the solver — the type system and the matcher, the
two pieces that decide whether a rule application is legitimate, are **466 lines
together** (`type_checker.cpp:91–556`).

Note what is *not* true of any of it: **neither checker is verified.** ethos is
an efficient, independent, unverified C++ program, and independence is not
verification even though the two are constantly conflated. Getting that
distinction right matters enough that
[`docs/kernel-of-cvc5.md`](docs/kernel-of-cvc5.md#prior-art-and-what-each-guarantee-actually-is)
tabulates what every comparable tool actually proves — cake_lpr, SMTCoq,
lean-smt, bv_decide, IsaSAT, versat, Carcara — because "verified" names at least
five different guarantees across that list, with trusted bases ranging from *a
theorem about the machine code* down to *nothing at all*.

## The language

**Lean 4** is the provisional language for the first experiment, because it
can use Logos directly. Rust remains an option for an untrusted search core;
its suitability depends on measurements, not on the checker's implementation
language alone.

A dependent return type can require evidence for each successful answer, but it
does not prove termination, successful search on every input, or correct proof
serialization. CPC certificates must survive at runtime; a Lean proposition
alone can be erased during compilation. The design notes distinguish these
obligations.

Alternatives and their tradeoffs — Rust + Verus, F\* → C, Isabelle/HOL
+ Sepref, Rocq — are in [`docs/language.md`](docs/language.md), along with what
would change the decision.

## On the name

`telos` — τέλος — *end, completion, purpose*; Aristotle's final cause, the "that
for the sake of which". The design is asked backwards from the end state, which
is what the word is for.

## Status

Design notes, kept as the record that preceded the implementation in
[`ajreynol/telos`](https://github.com/ajreynol/telos). The measurements are of
other people's trees, namely cvc5, ethos and logos. The only design claim tested
against an implementation is the authoring half of I3, by the rewriter
described [above](#what-was-built). The notes below still describe the
proposal and have not been rewritten to match it.

- **[`docs/logos.md`](docs/logos.md) — the foothold: what Logos is, what it weighs, what its guarantee is, and eight lessons. Start here**
- [`docs/kernel-of-cvc5.md`](docs/kernel-of-cvc5.md) — what the kernel is today, what "defining" it means, and what each comparable tool's guarantee actually is
- [`docs/design.md`](docs/design.md) — the five inversions, and the risks in each
- [`docs/language.md`](docs/language.md) — the decision record
- [`docs/TODO.md`](docs/TODO.md) — next steps, ordered, with what each produces

The same standard as the rest of the repository applies: **a number here comes
from something that ran**, and everything else is labelled as a design note.
