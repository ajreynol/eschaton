# Next steps

The plan consumes [Logos](logos.md) as the checker and tests the producer side.
T2 has been carried out, outside this repository, as a verified rewriter and
preprocessor in [`ajreynol/telos`](https://github.com/ajreynol/telos), read at
`5661141`. The other tasks are proposals. Source counts refer to the revisions
in the linked notes. Read the correctness boundary before choosing a fragment.

Ordered by **how fast each one could kill the project**, the selection rule
[dokimasia's stance](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-stance)
applies to finding holes: optimise for the latency of the answer, not for how
much work it represents.

| | task | tests | status | produces |
| --- | --- | --- | --- | --- |
| **T1** | read Logos properly | nothing — the prerequisite for every other row | done in practice by the implementation | knowing what is already done |
| **T2** | a proof-producing rewriter for one theory | **[I3](design.md#i3--rewrites-prove-themselves-as-they-fire)** | built, for many theories; comparisons not run | the answer to whether telos's central claim holds |
| **T7** | one piece of search, ported the same way | whether the approach extends beyond simplification | not started | the evidence the proposal now most needs |
| **T8** | runtime cost against cvc5 | whether certification is affordable | not started; performance work has begun | the number that decides usefulness |
| **T9** | a Rust version of one telos component, proved equivalent through Aeneas | whether telos can be the specification for a fast port | not started | a runtime answer that keeps the guarantee |
| **T3** | a CPC corpus, run through **both** Ethos and Logos | nothing — it is an oracle | not started | agreement data, and the first `incomplete` census |
| **T4** | the static `incomplete` question | nothing | not started | a dokimasia-shaped analysis of a Lean development |
| **T5** | differential-test Ethos's evaluator against Logos's compiled semantics | nothing here — but it may find a real defect | not started | possibly a soundness finding, for somebody else's register |
| **T6** | measure Ethos's real TCB | nothing — it corrects a number | waiting on [`D9`](../../../docs/discussion.md) | an exact figure to replace a file-level estimate |

**T7 first now.** T2 returned favourably for simplification, so the question
that could still kill the project is whether search can be ported the same way.

---

## T1 — Read Logos properly

[`logos.md`](logos.md) is an outside reading of Logos's README, scripts and
source counts. The implementation has since consumed Logos as a library, which
is the practical form of this task. What still repays reading:

- **`Cpc/SmtModel.lean`** — 1,602 lines at the referenced revision, the core of
  the specification. If the specification is wrong, nothing downstream means
  anything, and it is the one place where "the proof checks" is not an answer.
- **`docs/modularity.md`** — written for a second checker: the checker layer is
  byte-identical between `Cpc` and `CpcMini`, the core depends on one signature
  symbol (`and`), and the cost of a new checker is dominated by the semantics
  layer. It also warns that Lean's positional arm names (`__smtx_model_eval.eq_9`
  is `and` in Cpc and `imp` in CpcMini) make a proof reusable by accident only.
- **`Cpc/Api.lean`, `ApiChecks.lean`, `ApiCorrect.lean`** — how the theorem
  connects to the string the executable receives.

A full Logos proof build takes **over two hours** and is not in its CI.

## T2 — A proof-producing rewriter for one theory

> **Status at `ajreynol/telos` `5661141`: built, beyond one theory.** A
> verified rewriter covers Booleans, arithmetic, UF, bit-vectors, arrays,
> datatypes, sets in the safe-mode fragment, part of strings, and Boolean
> quantifiers. A verified preprocessor follows it. It takes its rules from CPC
> rather than writing them in the host, and needs no proof work per rule.
> Agreement with cvc5 is measured by differential fuzzing. The
> [README](../README.md#what-was-built) summarizes what was built and where it
> departs from the plan.

**What is still open in T2** is the comparison it was meant to make, using the
[parent README's terms](../../../README.md#three-approaches-to-rewriter-maintenance):

| baseline | what it is | the number |
| --- | --- | --- |
| **writing the rule at all** | `theory/booleans/rewrites` at cvc5 `aee8742404` | 41 rules in 76 lines — **1.9 lines per rule**, with no proof obligation attached |
| **generated rules in a hybrid rewriter** | marking a RARE rule `:exec` on `ajreynol/cvc5` `rdbExec` at `4585967004` | **one token per existing rule**; expressing new rules and maintaining the generator are separate costs |

telos's cost per rule is zero proof work, because it writes no rules. Its cost
moved into porting strategies, writing the static and semantic justifications
used for speed, and keeping the generated rule table in sync with Logos. That
cost has not been measured against either baseline. **A rewriter that beats
the 2022 objection and loses to the hybrid `:exec` prototype is a negative
result for telos**, and should be recorded that way.

## T7 — One piece of search, ported the same way

The proposal's open question. Pick the smallest piece of search whose output a
CPC proof can justify — a CDCL(T) core for one theory, or conflict explanation
for UF — port it from cvc5 with agents under the same discipline, and measure:

1. whether every derived fact can be justified by a checked CPC step or a
   once-proved static or semantic step;
2. how much of the port needed new Lean proof, as opposed to ported control
   flow;
3. the runtime cost against cvc5 on the same inputs.

The broader plan, including deriving facts in a no-proof mode where the
evidence is erased at compile time, is in telos's own
[`solver-plan.md`](https://github.com/ajreynol/telos/blob/566114153c36bff8145224bf8831250c079a51f3/docs/solver-plan.md).

## T8 — Runtime cost against cvc5

Runtime was a non-goal when the rewriter was built, and fuel replaces
termination proofs. Measure rewriting and preprocessing time against cvc5 on
the fuzzing corpora and on benchmark preprocessing, and separately the cost of
proof printing. Telos's profiling script reports where rewriting time goes;
that is a starting point, not a comparison.

## T9 — A Rust version of one component, related through Aeneas

Derive a Rust implementation of one verified telos component — the Boolean
rewriter is the smallest — keeping inside the Rust subset that Charon and
Aeneas translate. Prove its translation equivalent to telos's Lean, so that
`Telos.rewrite_correct` carries over, and measure its speed against telos and
against cvc5. [Pathos](https://github.com/daniel-larraz/pathos)'s CpcMini pilot,
at `af0fe49`, is the nearest precedent and the place to learn what the
translation forbids. This is [`cvc6`](../../cvc6/README.md#working-backwards-from-telos)'s
backwards route, tried at the smallest scale.

## T3 — A CPC corpus, through both checkers

Run cvc5 with `--dump-proofs --proof-format=cpc` over a benchmark set, then run
**both** `ethos` and `logos` over the result. Split the corpus by
`--safe-mode=safe` against unrestricted, along the line
[the contract](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-contract)
cares about. Three measurements:

- **the oracle** for T5, and for proofs printed by T2 and T7;
- **the `incomplete` census.** `incomplete` means Logos accepted the proof but
  the proof mentions something its specification does not model. This measures
  translation coverage, not semantic conformance. Record parser errors and
  timeouts separately;
- **Ethos/Logos disagreement.** Any proof one accepts and the other rejects is
  worth investigating after confirming the signature, supported syntax and
  assumptions. Neither direction alone establishes a soundness defect.

## T4 — The static `incomplete` question

T3 answers *how often* per input. The dokimasia question is the other one:

> **Which CPC proofs could Logos ever return `incomplete` on?**

The side conditions are `TranslatableAssumptionList` and `CmdListTranslationOk`
in `Cpc/Proofs/Assumptions.lean`. They are `Decidable`, so the question is about
the reach of `__eo_to_smt` rather than a heuristic. Start with Logos's
conformance documentation and distinguish an unsupported translation from a
restriction on the model class. Only the first need produce `incomplete`.

## T5 — Ethos's evaluator against Logos's compiled semantics

Logos proves supported rules sound under its compiled semantics. It does not
establish that Ethos's C++ evaluator (`TypeChecker::evaluate`, the 56 `eo::`
builtins, `evaluateProgramApp`) agrees with what `ethos-eoc` compiles the same
signature into. A divergence may change acceptance; a disagreement needs
reduction and semantic analysis before it is called a defect. T3's corpus makes
this cheap to look for.

**Where it belongs.** This is a *soundness* question about Ethos, and dokimasia
is [completeness, not soundness](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-stance).
A concrete finding goes through the parent's reporting process, with evidence
and human review; this task does not authorize reporting it externally.

## T6 — Measure Ethos's real TCB

[`kernel-of-cvc5.md`](kernel-of-cvc5.md) splits Ethos's source by file, not by
dependency closure. Dokimasia's `dokimasia_analyzer.tcb` computes the closure
needed, but its seed sets are cvc5 paths. The parent asks in
[`D9`](../../../docs/discussion.md) whose measurement it is. Deprioritised
either way: it sharpens a comparison rather than deciding anything.

---

## Then: a solver

If T7 comes back favourable, the target is a solver whose answers are justified
in CPC, with the configuration constraints of
[I4](design.md#i4--safe-mode-is-a-type-not-a-list) holding by construction.
Someone else maintains the goalposts:

> **telos succeeds when Logos says `correct` for the intended input in the agreed fragment.**

The input correspondence and chosen fragment are part of the criterion. Record
`correct`, `incorrect`, `incomplete`, parser errors and resource limits
separately.

## Not doing

- **Writing a kernel.** Logos is the kernel.
- **Writing a full CDCL(T) solver before T7.** The smallest piece of search
  comes first. Starting with a whole solver is how research projects become
  abandoned codebases.
- **Verifying the search**, under the current design. See
  [I5](design.md#i5--soundness-is-the-kernels-job-completeness-is-the-type-systems).
  Verifying code directly is [`cvc6`](../../cvc6/README.md)'s bet, not this
  one. Static and semantic steps are verified code, but they justify individual
  steps; they do not verify the strategy that chooses them.
- **Claiming anything about cvc5, Ethos or Logos.** A fact about any of them
  goes into that project's register under its own name, with the same
  evidence standard.
- **Presenting the proposal as an implemented solver.**

## The honest cost

T7 is weeks and could fail. T8 and T3 are days. T4 is weeks. These are
planning estimates, not measurements. The progressive stance from
[dokimasia's kernel wishue](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#a-kernel-you-can-argue-about)
applies unchanged: **every degree is worth having, and there is no finish
line.**
