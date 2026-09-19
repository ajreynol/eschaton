# Five inversions

Each section takes one thing dokimasia measures about cvc5, proposes an
explanation, and states what a solver built the other way round would do
instead. Each also carries **what would make it wrong**, because a design note
whose author cannot say how it fails is an advertisement.

There is no implementation here. The cvc5 examples refer to `aee8742404`;
the Logos source counts refer to `56c7b409`. These are source measurements,
not benchmark results. The proposed benefits are hypotheses; see
[Logos](logos.md#what-its-guarantee-actually-is) for the correctness boundary.

---

## I1 — The answer carries its certificate

**What cvc5 does, and why.** `TheoryInferenceManager` declares
`ProofGenerator* pg = nullptr` as a default argument, and `conflict(TNode,
InferenceId)` and `lemma(TNode, InferenceId, LemmaProperty)` take no generator
at all ([H6](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#proof-hygiene)). So

```cpp
d_im.lemma(lem, InferenceId::ARITH_MY_NEW_INFERENCE);
```

compiles, runs, and silently produces a trust step. **The ergonomic path is the
proofless one.** The interface permits proof production to be optional; the
proposed design makes evidence mandatory for successful answers.

Downstream: 79 inferences fall through to a trust step by construction
([i-22](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)), 8 trust steps are built with `TrustId::NONE`
and so cannot be attributed at all ([i-9](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)), and
`--check-proofs-complete` exists to discover at runtime, one benchmark at a
time, which of these a given input reached.

**The inversion.** Require a runtime certificate tied to the input. This is
schematic Lean notation; the certificate format and checking predicate remain
to be defined:

```lean
inductive Answer (phi : Formula) where
  | unsat   (certificate : CpcProof) (checked : ChecksAgainst phi certificate)
  | sat     (M : Model) (h : Satisfies M phi)
  | unknown (r : Limitation)
```

A successful constructor requires evidence. A certificate is data that must
survive compilation; Lean erases propositions from compiled code, as its
[reference explains](https://lean-lang.org/doc/reference/latest/The-Type-System/Propositions/). Exporting CPC,
checking the parsed assumptions against `phi`, and validating the certificate
are separate obligations. A type annotation on an untrusted search routine
does not establish those obligations on its own.

A closed `Limitation` type can require a reason for `unknown`. It cannot prove
that search terminates, succeeds on every input, or reaches every annotated
path. Proof support is intended to be mandatory for successful answers, while
resource exhaustion remains a legitimate outcome.

**What would make it wrong.** Authoring and runtime costs. A lazily constructed
certificate DAG may reduce the cost, but replacing it with an opaque unchecked
token would abandon the guarantee. The first experiment must measure both
certificate construction and independent checking.

---

## I2 — One definition of the calculus

**What cvc5 does, and why.** The calculus is stated three times: as LaTeX
`\inferrule` blocks in `cvc5_proof_rule.h`
([H9](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#proof-hygiene)), as
C++ checkers registered with `ProofChecker`, and as 620 `declare-rule`s in the
Eunoia signature. They are written by different people at different times, and
[`dokimasia.signature`](https://github.com/ajreynol/dokimasia/tree/main/dokimasia/signature/) exists precisely because
they can disagree — it found one
([i-21](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register): `SUBS`'s documentation omits an argument its
checker reads).

[R1](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#r1--emit-the-tables-cvc5-already-has) is dokimasia's request: *emit the tables cvc5 already has*. It is an
ask because the tables are recovered by parsing C++, leaving their accuracy
dependent on that parser.

**The inversion.** There is one inductive definition of the calculus. The
checker is a function over it, the printer is a function over it, the
documentation is generated from it, and the signature *is* it.
`dokimasia.signature` has nothing to check because there is nothing for the two
halves to disagree about — the class of defect it looks for is not expressible.

**The checker side has an implementation.** [Logos](logos.md) compiles its entire
calculus — 591 rules, the term language, the parser configuration, the
translation to SMT-LIB — out of `Cpc.eo` with `ethos-eoc`, keeps the per-rule
proofs across regeneration, and has a CI group that fails when generated code
drifts from its source signature. A rule added to CPC shows up as a
`sorry` stub; a rule whose *statement* changed keeps its old proof and fails to
build. Both failures are loud and distinct by design. telos inherits this rather
than redesigning it.

This is the least novel idea in the document and the most reliably valuable.
[H11](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#proof-hygiene)
already observes the pattern working inside cvc5: the RARE→`ProofRewriteRule`
correspondence is *exact in both directions, and holds because it is generated*.
"Nobody maintains it, so it cannot drift" is the whole design principle,
applied once. telos applies it everywhere.

**What would make it wrong.** Nothing, in principle — but note what it costs.
A single definition means the checker's representation is the printer's
representation is the solver's representation, and those three have genuinely
different performance requirements. cvc5 states the calculus three times partly
by accident and partly because a `switch` over an enum is fast and an LF-style
term is not. Expect to need a compilation step, and expect that compilation step
to become a thing that can drift.

---

## I3 — Rewrites prove themselves as they fire

This proposes a **proof-producing rewriter**, compared below with a **generated
rewriter** and cvc5's **proof-reconstructing rewriter**. The names follow the
[parent README](../../../README.md#three-approaches-to-rewriter-maintenance).

**What cvc5 does, and why.** Its current approach is a proof-reconstructing
rewriter: the rewriter is a black box, and proofs of
rewrites are recovered afterwards by searching a database of RARE rules for
something that explains what the rewriter did. This is deliberate and argued in
print — Nötzli et al., the FMCAD paper, §I:

> *"we propose an alternative approach that does not rely on instrumenting the
> original rewriter … instrumenting this code to additionally produce proofs
> makes it even more complex and makes it harder to add new rewrite rules."*

The consequence is [i-4](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register), a reconstruction limit recorded by dokimasia: reconstruction is a recursive
search with **no termination guarantee** — applying a rule spawns sub-problems
(its preconditions, and the gap between its instantiated RHS and the target)
that are not provably simpler than the goal. The paper says so outright, which
is why `--proof-rewrite-rcons-rec-limit` exists at all. Measured: 92–95% of
rewrite *steps* reconstruct, but only **20–22% of proofs are fully
fine-grained**, because one coarse step spoils a proof.

**A proof-reconstructing rewriter is very hard to verify statically.** A
guarantee that every rewrite has a reconstructed proof must relate the
handwritten implementation to the separate rule database and show that the
bounded search succeeds, including on conditions. Checking the proofs it finds
establishes something narrower: those particular steps are justified.

So: **whether a cvc5 proof is complete depends on how long a search is allowed
to run.** That is a strange property for a contract to have, and
[dokimasia's kernel wishue](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#a-kernel-you-can-argue-about) is right that a kernel has to
confront it rather than inherit it.

**The inversion.** A schematic result type carries the rewritten term and a
runtime certificate of semantic equality:

```lean
structure RewriteResult (t : Term) where
  result : Term
  certificate : CpcProof
  checked : ChecksEquality t result certificate
```

The format and `ChecksEquality` predicate remain to be defined, as in I1.

For rules supported by this interface, justification is constructed with the
rewrite rather than recovered by a later search. This does not remove budgets
from solver search, conditional-rule discharge, or unsupported rewrites.

**The FMCAD paper's objection applies here.** Its concern is that instrumenting
a rewriter by hand makes it complex and makes rules harder to add. The claim is that the
objection is *about the language*, not about the architecture. A declarative
rule in a dependently typed host elaborates to **both** the rewrite and its
justification from one source, so there is no second thing to maintain and no
second place to get it wrong. Writing a rule stays as cheap as writing a RARE
rule; the proof is a derived artifact, not a parallel obligation.

Generating the rewrite and justification together may prevent some
[correspondence defects](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-rare-correspondence).
It does not ensure that a rule is ever selected, that its preconditions are
reachable, or that the generator implements the intended rule.

### Generated rewriters and the hybrid in `rdbExec`

A **proof-producing rewriter** returns its result with its justification;
telos proposes expressing that interface in a dependently typed host.
A **generated rewriter** takes its executable code from declarative proof rules.
The rewrite is a rule application, so its proof can name the rule directly.
These approaches compete on maintenance cost and can overlap in an
implementation. A **proof-reconstructing rewriter** searches for the proof
afterwards; rule conditions and fallback cases can still need that search in
a hybrid.

**The branch advocates a hybrid.** `ajreynol/cvc5` branch `rdbExec`, at `4585967004`,
branched from cvc5 `5cc03f4b95` — an exploratory branch, not a shipped feature,
not a cvc5 position, and read here at that commit. It combines generated rules
with the handwritten theory rewriters and retains proof reconstruction. Its
generated portion runs when the theory rewriter leaves a term unchanged. The shape:

| piece | what it does |
| --- | --- |
| an `:exec` attribute on a RARE rule | marks it for compilation; parsed in `rw_parser.py`, carried on `Rule.is_exec` |
| `rewrite_db_exec_printer.cpp` | **generates the matcher**: 1,088 lines that print straight-line C++ testing the shape of a term, emitted by `-o rare-db-exec` and installed by `contrib/install-rare-rewrites` |
| `rewrite_db_exec.h/.cpp` | the generated database. Its own header: *"The bodies of the methods of this class are generated, not written by hand… Do not edit that file; edit the RARE rules and regenerate it"* |
| `theory/rewriter.cpp` | applies `:exec` rules **as a last resort, when the theory rewriter leaves a term unchanged** |
| `TrustId::THEORY_REWRITE_EXEC` | the step the rewriter records, carrying the id of the rule that fired |
| `ProofPostprocessDsl::proveWithRule` | *"Since we know which rule proves eq, we apply it directly rather than searching"* |

**Why this bears on I3 and not merely on cvc5's engineering.** Dokimasia's
[i-4](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register) is that
proof completeness depends on a search budget. That search recurses on two
things: the **precondition** of a conditional rule, and the **gap** between the
instantiated right-hand side and the target. For an `:exec` rewrite the second
one is gone — not because the search got faster, but because the rewriter
produced σ(v) itself, so there is no gap to close. **The conditions remain**:
the branch adds them as trusted steps *"which this class reconstructs in
turn"*, and those go back through the same bounded search.

So the honest statement is that this route **narrows i-4 rather than dissolving
it**, and does so without a new language, a new kernel or a new solver.

**Dokimasia confirms that mechanism and bounds it twice**, read at dokimasia
`1eeae9b`, and the second bound is a correction telos should carry rather than
soften. `i-4` is a claim about the **procedure**, so removing one recursion
source for the compiled rules leaves its termination status exactly where it
was — *"a procedure with no termination argument that now needs the budget less
often still has no termination argument."* And the budget is spent per
reconstruction rather than per rule, so six compiled rules are a smaller
constant and *"a smaller constant is not an argument."* **This cuts both ways
for telos.** It weakens the branch as a rebuttal of I3, because the branch does
not retire the obligation telos claims to discharge by construction; and it
weakens I3's own framing, because *narrowed* and *dissolved* read alike in a
design note and only one of them is a termination argument. What I3 would have
to deliver is the argument, not a smaller constant.

**Dokimasia also names a gain that is not I3's.** For a rule compiled into the
rewriter, the RARE rule and the generated C++ stop being two statements of one
fact, so
[i-17](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)'s
*established only by runtime search* does not describe it — the strongest form
of the test its pages call `E1`, arriving as a by-product. The cost is that the
recorded rule is a trust step, trusted when made and reconstructed afterwards,
which moves the work into its `trust` census. **That by-product is bounded by
the marker**, which is the six rules counted below and not the technique.

**Read the branch's own caution, which is the part telos should take most
seriously.** Three things it says about itself:

- **`proveWithRule` can fail and fall back.** *"This fails if the rule does not
  apply to eq as it stands, e.g. because eq uses an encoding of terms that
  differs from the one the RARE rules are stated over. The caller falls back on
  `d_rdbPc` in that case."* The encoding seam does not disappear; it is handled
  where it was.
- **Termination moved into the rule set.** A condition is rewritten like any
  other term, so it may re-trigger the rule whose condition it is. The branch
  breaks the cycle by abandoning a match whose condition is already being
  checked, and says why the bookkeeping is unconditional rather than assertions-
  only: *"a cyclic condition originates from the RARE rule set"*. **A generated
  rewriter makes the rule set responsible for termination**, which is an
  obligation telos would inherit in full and has not costed.
- **The generated code is checked, not trusted.** `checkMatch` re-instantiates
  the left-hand side and compares: *"It is what makes the generated code
  checkable rather than trusted."* That is the same discipline I2 praises Logos
  for, arrived at independently and inside C++.

**At what scale it has been tried.** Six rules carry `:exec` at `4585967004` —
five in `theory/strings/rewrites`, one in `theory/bv/rewrites` — out of 321 RARE
rules in cvc5 `aee8742404`. That is a prototype, and the six are not the easy
ones: they were chosen to cover an indexed operator, a conditional rule with a
`:list` variable sandwich, and three rules sharing a prefix tested in one
traversal. One of them, `re-star-star`, **replaces a hand-written case deleted
from `SequencesRewriter`**, which its regression calls *"the intended migration
path: a rewrite implemented by hand is deleted in favour of the RARE rule"*.
**That single deletion is the existence proof**, and it is worth more to this
argument than the other five together.

**What it does to telos.** I3's claim was never that proofs-with-rewrites is
the only route to dissolving i-4; it was that a dependently typed host makes it
cheap. This branch is a competing bid on cost, from inside a working solver,
with no rewrite of anything. Telos's claim survives only if elaboration in a
dependently typed host is cheaper *per rule* than marking a rule `:exec` and
regenerating — and `:exec` is one token. **That is now the number T2 has to
beat**, and it is a much harder target than the FMCAD paper alone set.

**What would make it wrong.** This is the inversion most likely to be wrong,
and it should be tested before anything else is built on it. Four ways it
fails:

- the elaboration is not as free as claimed, and generating the justification
  turns out to need per-rule manual work — in which case the FMCAD objection
  stands and telos has learned something worth writing down;
- the rewriter needs optimisations that are not expressible as rule
  applications — caching, in-place mutation, normalisation strategies that are
  not confluent — and those are exactly the parts that resist carrying a proof;
- performance. Building a proof term for every rewrite step, when a solver
  performs millions of them, may dominate. cvc5's design avoids this cost by
  construction and telos would be paying it on every step;
- **A hybrid using generated rules gets there first, and more cheaply.** If
  marking rules `:exec` closes the same gap inside a solver that already works,
  the dependently typed host is buying a guarantee nobody needed at a price
  nobody wanted to pay. This is the failure mode with a working prototype
  behind it, and it is the one to test against.

**The cheapest test:** implement a proof-producing rewriter for one theory, in a fragment
where cvc5's RARE coverage is already good, and measure both the rule-authoring
cost and the runtime against generated rewriting in the hybrid `:exec`
prototype and the proof-reconstructing baseline. Include the hybrid's remaining
handwritten work and reconstruction costs. The experiment does not require a
solver.

---

## I4 — Safe mode is a type, not a list

**What cvc5 does, and why.** `--safe-mode=safe` promises no feature "that does
not have full proof and model support", and delivers it at *runtime*:
`SetDefaults::setDefaultsPre` turns features off by name, and
`NoOpTheoryRewriter` throws `SafeLogicException` if a disabled theory is reached
anyway. The list is hand-maintained ([i-5](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)), and
`stringLazyPreproc` already escapes it — it declares `no_support = ["proofs"]`,
defaults to `true`, and neither mechanism disables it
([i-2](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)). The unsafe code is compiled, linked, and one
missed guard away.

[R8](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#r8--safe-mode-as-a-build-time-property) asks for
the build-time version: make the safe build not *contain* the unsafe code, so a
missed guard is a link error. `ENABLE_SAFE_MODE` exists and prunes almost
nothing — five files in `src/` mention `CVC5_SAFE_MODE`, two of them only to
reword an error message.

**The inversion.** The solver core is parameterised by the set of enabled
features, and a feature with no proof support does not typecheck in the safe
instantiation. Not pruned at link time — *rejected at type-check time*, with the
error naming the feature.

Concretely, proof-producing features would require a certificate interface
when registered in the safe configuration. Operational limits such as timeouts
must still permit `unknown`; making every `Limitation` uninhabited would
incorrectly equate proof support with total search. The type-level interface is
an experiment, not a demonstrated replacement for runtime checks.

This is the second of [dokimasia's two wishues](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#a-safe-build-that-cannot-be-unsafe)
— *a safe build that cannot be unsafe* — reached by construction rather than by
progressive pruning. And it makes the consistency check that page proposes
(the runtime disable list and the build-time exclusion list must agree)
vacuous: there is one list and it is the type.

**What would make it wrong.** Feature configuration in a real solver is not a
clean lattice. Options interact, some features are partially proof-producing,
and "does this have proof support" is often *fragment*-dependent rather than
feature-dependent — which is exactly [i-15](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register): the
supported fragment is not expressible as a list of kinds, because two of the
options safe mode disables gate a *logic* and a *type* rather than a kind. A
type-level encoding that cannot express that is a worse model than cvc5's list,
not a better one.

---

## I5 — Soundness is the kernel's job; completeness is the type system's

This is the one that makes the other four affordable, and it is a restatement of
dokimasia's own stance rather than a new idea.

[dokimasia's stance](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-stance) is explicit: **completeness, not
soundness.** Not *is this proof step valid* but *is there a path that produces
no proof at all*. It can take that stance because something else handles
soundness — `ethos` checks the proof, so dokimasia does not have to.

Telos proposes the following split:

| obligation | proposed mechanism | limit |
| --- | --- | --- |
| validity of a supported CPC refutation | Logos's soundness theorem | parsed assumptions under Logos's semantics, with the trust obligations in [Logos](logos.md#what-its-guarantee-actually-is) |
| evidence accompanying a successful answer | a certificate-bearing return type and validation at the boundary | not termination or mathematical completeness |
| finding an answer | untrusted search | may fail, time out or return `unknown` |

The heading's “completeness” means proof coverage for successful answers only.
An algebraic data type does not prove that every valid input can be solved.
Any verdict other than `correct` must be investigated and classified as a
producer defect, unsupported fragment, resource limit or possible checker
issue; it is not automatically a defect in one particular tool.

**The search is unverified under this design.** Independent certificate checking
is what prevents a search error from becoming an accepted refutation, provided
the input correspondence and semantic boundary hold.

IsaSAT and versat provide examples of the alternative. They
verify the *search* itself, and both show what that costs: IsaSAT is the fastest
verified SAT solver by a wide margin and still nowhere near CaDiCaL, and
versat's guarantee turns out to be soundness of UNSAT only — not completeness,
not termination, with some checks deferred to run time. Neither emits a
certificate, so neither gives a third party anything to check. Meanwhile
`cake_lpr`, SMTCoq, `bv_decide` and `lean-smt` all verify or kernel-check the
*checker* and leave the solver alone, and all four exist and work. The
per-tool guarantees are tabulated in
[`kernel-of-cvc5.md`](kernel-of-cvc5.md#prior-art-and-what-each-guarantee-actually-is);
they differ more than the shared word "verified" suggests.

The intended claim is a solver with a verified checking function and mandatory
certificates for successful answers. No solver here establishes that claim yet.

**What would make it wrong.** Two things. First, the kernel is only small if K4
and K5 stay out of it — and 1,009 lines of builtin operations plus 248 signature
`program`s say they will not. Second, "the search is untrusted" is only
comfortable if a rejected proof is *diagnosable*; a solver that emits a proof
the kernel rejects, with no way to attribute the rejection, is worse than one
that emits nothing. Carcara's elaborator work is the place to look for what that
costs.
