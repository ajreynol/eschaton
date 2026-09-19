# Competing approaches

The [README](../README.md#three-approaches-to-rewriter-maintenance) defines the
shared terms **generated rewriter**, **proof-producing rewriter** and
**proof-reconstructing rewriter**, and is the ground truth for them. They name
competing ways to maintain a rewriter and its proofs, usable across the broader
solver proposals below.

*[Three approaches to rewriter maintenance](#three-approaches-to-rewriter-maintenance)
below restates those definitions at length. **Nothing compares the two copies**,
so a reader who finds them disagreeing should take the README's.*

## Broader solver approaches

Three bets on how to get a better SMT solver than the ones we have. **Proof-first
design is the current preference; none has been tested here.** Its existing
verified checker in Logos and concrete next experiment make it the most promising
path to explore. They differ on what they keep from cvc5 and on who does the writing;
what already exists in public for each is
[`related-work.md`](related-work.md).

| | keeps from cvc5 | written by | the bet | dies if |
| --- | --- | --- | --- | --- |
| Proof-first design | the calculus, and parts of the internal proof checker | people | designing proof production with search may reduce reconstruction gaps | a proof-producing rewriter costs too much, **or a hybrid using generated rules closes the same gap more cheaply** |
| Automated maintenance | all of it | agents | the design is the asset; mechanize the upkeep and the build order stops mattering | upkeep does not outrun accumulation |
| Agent-built solver | nothing | agents | solvers are scarce because people are, so make architecture cheap to vary | the hard parts are exactly the parts that do not automate |

**Proof-first design and automated maintenance are the real disagreement.**
The former hypothesizes that designing proof production with search is cheaper
than reconstructing proofs afterwards; the latter asks whether improving the
existing implementation is cheaper still. The measurements do not establish
that development order causes the gaps, and the approaches could complement
each other.

**Building a new solver with agents is arguing about something else.** It shares
automated maintenance's method and proof-first design's willingness to start
over, and it is the only one whose bet is about *production cost* rather than
correctness — which is also its weakness: nothing in it
produces a reason to believe the output. The LLM2SMT study reports
competitive QF_UF solving with much more limited certification; see the
[source summary](related-work.md#agent-built-solvers).

## Three approaches to rewriter maintenance

A **proof-producing rewriter** returns the rewritten term and its equality
certificate together. The proposal here uses a dependently typed host
to derive both from one rewrite definition. Its maintenance claim is that adding
or changing a rule needs little separate proof work. That claim remains untested.

A **generated rewriter** makes declarative proof rules the source of executable
rewrite code. A maintainer edits the rule and regenerates the matcher; a step
records the rule that fired so the proof producer can apply it directly.

A **proof-reconstructing rewriter** is cvc5's current approach: the handwritten
rewriter produces a result, then a separate reconstructor searches proof rules
for an explanation. This lets the rewriter evolve without constructing a proof
at each step, at the cost of maintaining the correspondence and the search.

**This third approach is very hard to verify statically.** Proving the rules
sound is only part of the task. To guarantee a proof for every supported
rewrite, one must also show that the rules cover the handwritten code's
behaviour and that reconstruction finds the proof within its budget, including
conditional-rule obligations. Those obligations are not necessarily simpler
than the original goal. A checked proof establishes the soundness of the step
it justifies; it does not establish this global reconstruction guarantee.
Generated and proof-producing rewriters expose more of that correspondence
directly, but neither is automatically verified by its architecture.

**The `rdbExec` branch advocates a hybrid:** generated rules alongside
handwritten theory rewriters and proof reconstruction. At the revision read
here, generated rules run when the theory rewriter leaves a term unchanged;
the branch does not aim to
replace all rewriting with generated code. Its prototype is in
[`ajreynol/cvc5` branch `rdbExec`](https://github.com/ajreynol/cvc5/tree/rdbExec),
read at `4585967004` from cvc5 `5cc03f4b95`. This is exploratory work in a
personal fork: six rules, no release, and no position of cvc5's.

These compete on the cost of maintaining rewrites with proofs, and can overlap
in an implementation. A proof-producing rewriter specifies what a rewrite must
return; a generated rewriter specifies where its executable code comes from;
a proof-reconstructing rewriter relies on later search. These terms do not
commit to a new solver, a programming language, or who writes the code.
The broader proof-first proposal must therefore justify its cost against
the hybrid use of generated rules inside the existing solver.

[`related-work.md`](related-work.md#where-a-rewrites-proof-comes-from--the-argument-all-three-bets-inherit)
records the prototype and the published proof-reconstruction baseline.

**This narrows the question rather than settling it, and the narrowing is
smaller than it looks.** An `:exec` rewrite removes one of the two things
reconstruction recurses on — the gap between a rule's instantiated right-hand
side and the target — and leaves the other, since rule conditions are still
reconstructed through the same bounded search. Dokimasia, which is the authority
on [`i-4`](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register),
confirms that mechanism and states two limits on what it buys, read at dokimasia
`1eeae9b`. `i-4` is a claim about the **procedure**, so narrowing it over the
compiled rules leaves its termination status exactly where it was: *"a procedure
with no termination argument that now needs the budget less often still has no
termination argument."* And the budget is spent per reconstruction rather than
per rule, so dropping one of its two consumers for six rules is a smaller
constant — *"a smaller constant is not an argument."*

**What `:exec` does change is a different register.** For a rule compiled into
the rewriter, the RARE rule and the generated C++ stop being two statements of
one fact, because the C++ is derived from the rule. Dokimasia reports this as
the strongest form of the direct test its pages call `E1`, arriving as a
by-product rather than as a test, and names the cost: recording the applied rule
as a trust step means the step is trusted when made and reconstructed
afterwards, which moves work out of its `rewrites` register and into `trust`.
**The by-product is bounded by the marker**, which six rules carry at
`4585967004`.

**It is also not dokimasia's `E4`.** That problem is about compiling the rule
database into the *reconstructor*; this branch compiles the *rewriter* — *"the
first attacks the search, the second removes the occasion for it."*

What nobody has measured is how far the second goes: how many of cvc5's RARE
rules could carry `:exec` — 321 of them at cvc5 `aee8742404` — and what the ones
that cannot have in common. **That measurement, not a new argument, is what
would move this comparison.**

## What proof-first design would keep

Proof-first design is **not** "rip the spine out of cvc5 and build around it",
though it is closer to that than the other two. It keeps the **proof calculus** — the
`Cpc.eo` signature cvc5 already emits against — **parts of the internal proof
checker**, and dokimasia's measurements of where cvc5's proof coverage has
holes. Its kernel is [Logos](https://github.com/cvc5/logos), a verified
checker in Lean that already exists; what this approach would build is the
producer. The search does not come across.

[Dokimasia's measurement](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#a-kernel-you-can-argue-about)
of cvc5 `aee8742404` identifies `ProofChecker` plus thirteen
registered theory rule checkers under `--check-proofs`: a compile closure of
179 files and 41,446 lines, about 8% of `src/`. This is a source dependency
measurement, not a minimal trusted-base proof. Which parts to keep is undecided.

Logos's [correctness statement](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/README.md#correctness)
concerns the assumptions its parser reads under its model
semantics. The parser, original-input correspondence and compiled execution
remain trust obligations. Its [conformance limits](https://github.com/cvc5/logos/blob/56c7b4098a8c5e7b170ab506fc88d18a913d3cb6/docs/smt-lib-conformance.md)
mean that a `correct` verdict alone is not a general SMT-LIB guarantee.
The first Boolean rewriter experiment must check the input correspondence as
well as the verdict. `incomplete` measures unsupported translations; it does
not detect every semantic mismatch or prove completeness of search.

The spine transplant proper — keep cvc5's core engine as a working artifact and
rebuild the architecture around it — is a fourth bet, unclaimed here rather than
rejected.

## How any of this gets decided

Each bet has a cheap experiment that could kill it, and none has been run: a
proof-producing rewriter for one theory; one agent-driven refactor of one cvc5
subsystem measured against the gaps it is meant to close; or a new theory
solver that passes somebody else's benchmark set. The preference for proof-first
design remains provisional until those experiments provide evidence to judge
the approaches.

**Compare a proof-producing rewriter with a generated rewriter on one theory.**
Measure manual work per rule, supported rewrites, certificate construction and
checking costs against the proof-reconstructing baseline. The 2022 paper argues
for that baseline on maintenance grounds; the hybrid `:exec` prototype tests
generation inside it. Measure both its generated rules and the work left to
handwritten rewriting and reconstruction. Marking an existing RARE rule `:exec`
adds one token, but that does not measure the cost of expressing a new
rule, handling its conditions, or maintaining the generator. **An experiment
that beats the paper and loses to the branch has not settled anything in
proof-first design's favour.**
