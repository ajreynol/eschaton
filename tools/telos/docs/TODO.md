# Next steps

The plan consumes [Logos](logos.md) as the checker and tests the producer
side. All tasks below are proposals; none is implemented here. Source counts
refer to the source revisions in the linked notes. Read the correctness
boundary before choosing an experimental fragment.

Ordered by **how fast each one could kill the project**, the same selection rule
[dokimasia `docs/goals.md`](https://github.com/ajreynol/dokimasia/blob/main/docs/goals.md) applies to finding holes: optimise for
the latency of the answer, not for how much work it represents.

| | task | tests | cost | produces |
| --- | --- | --- | --- | --- |
| **T1** | read Logos properly | nothing — it is the prerequisite for every other row | days | knowing what is already done |
| **T2** | a proof-producing rewriter for one theory | **[I3](design.md#i3--rewrites-prove-themselves-as-they-fire)**, the riskiest inversion, and the only one Logos does not settle | a week | the answer to whether telos's central claim holds |
| **T3** | a CPC corpus, run through **both** ethos and logos | nothing — it is the oracle, and a measurement this repository does not have | days | agreement data, and the first `incomplete` census |
| **T4** | the static `incomplete` question | nothing | weeks | a dokimasia-shaped analysis of a Lean development |
| **T5** | differential-test ethos's evaluator against logos's compiled semantics | nothing here — but it may find a real defect | days | possibly a soundness finding, for somebody else's register |
| **T6** | measure ethos's real TCB | nothing — it corrects a number | hours | an exact figure to replace a file-level estimate |

**T2 first.** It is the only item on the list that could invalidate the design,
it is a week, and nothing else depends on its outcome being favourable.

---

## T1 — Read Logos properly

Not a formality. [`logos.md`](logos.md) is an outside reading of the
repository based on its README, scripts and source counts. It is not a substitute for the four things that actually matter:

- **`Cpc/SmtModel.lean`** — 1,602 lines at the referenced revision; it is part of
  the specification a reviewer must understand, alongside `Cpc/Spec.lean` and
  the executable boundary. If the specification is wrong, nothing
  downstream of it means anything, and it is the one place where "the proof
  checks" is not an answer;
- **`docs/modularity.md`** — written for exactly telos's situation and specific
  about it: `Proofs/Checker.lean` is byte-identical between `Cpc` and `CpcMini`,
  the core depends on a single signature symbol (`and`), the cost of a new
  checker is dominated by the semantics layer, and *start from `CpcMini`*.
  Including the trap where Lean's positional arm names (`__smtx_model_eval.eq_9`
  is `and` in Cpc and `imp` in CpcMini) make a proof reusable by accident only,
  with a one-line discipline to adopt **before the first proof**;
- **`Cpc/Api.lean`, `ApiChecks.lean`, `ApiCorrect.lean`** — how the theorem
  connects to the string the executable receives. This is the part most
  verified-checker projects leave informal and this one does not;
- **`docs/smt-model-definitions.pdf`** — the intended write-up of the semantics,
  the specification and the checker.

Also worth knowing before planning anything: a full proof build is **over two
hours** and is deliberately not in CI, and `scripts/check-proof-hygiene.sh`
rejects `sorry`/`admit`/`axiom` textually with no build at all.

## T2 — A proof-producing rewriter for one theory

Compare a **proof-producing rewriter** with a **generated rewriter** and the
**proof-reconstructing rewriter** baseline, using the
[parent README's shared terminology](../../../README.md#three-approaches-to-rewriter-maintenance).
This task prototypes the first and uses the hybrid `rdbExec` design to measure
generation inside an existing solver. Record the work left to handwritten
rewriting and reconstruction as well as the generated portion. Measured proof
coverage is not a static guarantee that reconstruction always succeeds.

**The experiment that matters, and the producer question Logos does not answer.**

Logos supplies the checking function within its documented semantic boundary.
It says nothing about the *producer*, and
[I3](design.md#i3--rewrites-prove-themselves-as-they-fire) is the load-
bearing claim on that side: that a rewriter can return `(t', proof that t = t')`
at no meaningful cost to the author of a rewrite rule, dissolving
[i-4](https://github.com/ajreynol/dokimasia/blob/main/docs/issues.md) — the search budget that cvc5's proof completeness
currently depends on. The FMCAD paper argues against instrumenting the rewriter because of
the complexity and authoring cost. The claim is that a dependently typed host changes the arithmetic, and
**it has never been tested.**

Test it small. Pick one theory where cvc5's RARE coverage is already good — the
Boolean rules, `theory/booleans/rewrites` — and:

1. write its rules once, declaratively;
2. elaborate each into a rewrite *and* its justification;
3. check that the proof's parsed assumptions are the intended rewrite problem
   in the agreed fragment;
4. measure **how much per-rule manual work the justification needed** (the FMCAD
   objection) and **what building a proof term on every step costs at runtime**
   (the objection FMCAD did not have to make).

**Measure against two baselines, not one.** Both are in cvc5's trees and neither
has to be built for the authoring half:

| baseline | what it is | the number |
| --- | --- | --- |
| **writing the rule at all** | `theory/booleans/rewrites` at cvc5 `aee8742404` | 41 rules in 76 lines — **1.9 lines per rule**, with no proof obligation attached |
| **generated rules in a hybrid rewriter** | marking a RARE rule `:exec` on `ajreynol/cvc5` `rdbExec` at `4585967004` | **one token per existing rule**, with the matcher regenerated by `contrib/install-rare-rewrites`; expressing new rules and maintaining the generator are separate costs |

The second is the hard one, and it is the point.
[I3](design.md#i3--rewrites-prove-themselves-as-they-fire) sets out what
the generated portion does and does not close. **A proof-producing rewriter
that beats the 2022 objection and loses to the hybrid `:exec` prototype is a negative
result for telos**, and recording it that way is
the whole value of running T2 rather than arguing about it.

Three outcomes, all worth having: the claim survives; the authoring cost is real
and the FMCAD objection stands in a dependently typed setting too, which is an
interesting negative result; or proof terms on every rewrite step do not scale
and telos needs a different answer to i-4 than "make it not exist."

The natural target format is CPC, so that step 2's output is something
`logos` can be pointed at — which makes T3 a dependency in practice even though
it is not one in principle.

## T3 — A CPC corpus, through both checkers

Run cvc5 with `--dump-proofs --proof-format=cpc` over a benchmark set, then run
**both** `ethos` and `logos` over the result. Split the corpus by
`--safe-mode=safe` against unrestricted, so it divides along the line
[the contract](https://github.com/ajreynol/dokimasia/blob/main/docs/contract.md) cares about.

Three measurements are useful to this repository:

- **the oracle** for T2 and T5;
- **the `incomplete` census.** Logos returns three verdicts, and `incomplete`
  means it accepted the proof but the proof mentions something the SMT-LIB
  specification does not model. How often, and on what? That is a coverage
  number for translation coverage, not semantic conformance. Record parser
  errors and timeouts separately;
- **ethos/logos disagreement.** Any proof one accepts and the other rejects is
  worth investigating after confirming the signature, supported syntax and
  assumptions. Neither direction alone establishes a soundness defect.

## T4 — The static `incomplete` question

T3 answers *how often* per input. The dokimasia question is the other one:

> **Which CPC proofs could Logos ever return `incomplete` on?**

That is the same shape as everything in
[`docs/pipeline.md`](https://github.com/ajreynol/dokimasia/blob/main/docs/pipeline.md) — take the code, ask what it could
ever produce, with no benchmark in hand — pointed at a Lean development instead
of at C++. The side conditions are `TranslatableAssumptionList` and
`CmdListTranslationOk` in `Cpc/Proofs/Assumptions.lean`, and they are
`Decidable`, which means the question is about the *reach* of `__eo_to_smt`
rather than about a heuristic.

Start with Logos's conformance documentation and distinguish an unsupported
translation from a restriction on the model class. Only the first need produce
`incomplete`; the census must not be presented as a complete semantic audit.

## T5 — ethos's evaluator against logos's compiled semantics

Logos proves supported rules sound under its compiled semantics. That gives a
useful comparison point for the Eunoia programs those rules use, within the
model and trust boundary described in [Logos](logos.md).

What is *not* established is that the two implementations of Eunoia evaluation
agree: `ethos`'s C++ evaluator (`TypeChecker::evaluate`, the 56 `eo::` builtins,
`evaluateProgramApp`) against what `ethos-eoc` compiles the same signature into.
A divergence may change acceptance; the Lean theorem concerns only the Lean
implementation. A disagreement needs reduction and semantic analysis before
it is called a defect. T3's corpus makes this cheap to look for.

**Where it belongs.** This is a *soundness* question about `ethos`, and
dokimasia is [completeness, not soundness](https://github.com/ajreynol/dokimasia/blob/main/docs/goals.md#the-stance).
Any concrete finding belongs in the parent's reporting process, with evidence
and human review; this task does not authorize reporting it externally.

## T6 — Measure ethos's real TCB

[`kernel-of-cvc5.md`](kernel-of-cvc5.md) still says *"roughly 4,000 lines of
typing and evaluation, 2,300 of state, 3,600 of parsing"* and admits the split is
by file rather than by dependency closure. Two closures, seeded from
`TypeChecker` and from the parser entry point.

**A dokimasia task, not a telos one**, and one already implied by
[`TODO.md` G2](https://github.com/ajreynol/dokimasia/blob/main/TODO.md)'s *"extend the closure to the Eunoia seam."*
Deprioritised: it sharpens a comparison rather than deciding anything.

---

## Then: the producer side

Everything above is reading, measuring, or one experiment. What telos would
actually *build*, if T2 comes back favourable, is the part Logos does not cover:
a solver whose output is a CPC proof, structured so that
[I1](design.md#i1--the-answer-carries-its-certificate) and
[I4](design.md#i4--safe-mode-is-a-type-not-a-list) hold by construction —
the answer carries its certificate, and a proofless configuration does not
typecheck.

The target is precise and someone else maintains the goalposts:

> **telos succeeds when Logos says `correct` for the intended input in the agreed fragment.**

The input correspondence and chosen fragment are part of the acceptance
criterion. Record `correct`, `incorrect`, `incomplete`, parser errors and
resource limits separately. An `incomplete` census describes translation
coverage; it does not establish completeness of search or full SMT-LIB
conformance.

## Not doing

- **Writing a kernel.** Logos is the kernel. Rebuilding it would be the single
  most expensive way for this project to produce nothing.
- **Writing a solver**, until T2 returns. A CDCL(T) implementation is the least
  interesting and most expensive part of this, and starting there is how
  research projects become abandoned codebases.
- **Verifying the search.** Ever, under the current design. See
  [I5](design.md#i5--soundness-is-the-kernels-job-completeness-is-the-type-systems),
  and the price tag IsaSAT and versat put on the alternative.
- **Claiming anything about cvc5, ethos or logos.** telos is a design exercise.
  If it produces a fact about any of them — as T3 and T5 might — that fact goes
  into the appropriate register under the appropriate project's name, with the
  same evidence standard everything else here is held to.
- **Announcing it.** See the [notice](../README.md).

## The honest cost

T1 and T6 are days. T3 is days. T2 is a week or two and could fail. T4 is weeks.
Nothing here produces a solver.

These are planning estimates, not measurements. Consuming an existing checker
reduces the proposed scope, but does not settle certificate construction costs.

The progressive stance from [`docs/kernel.md`](https://github.com/ajreynol/dokimasia/blob/main/docs/kernel.md) applies
unchanged: **every degree is worth having, and there is no finish line.** T3
alone — a corpus through both checkers, with an `incomplete` census — would
justify the directory.
