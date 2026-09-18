# Related work

Public work bearing on the three bets in [`approaches.md`](approaches.md),
arranged by which one it helps or hurts. This is a reading list, not a
systematic survey. Results below are the authors' reports,
not runs here.

## Agent-built solvers

**[LLM2SMT: Building an SMT Solver with Zero Human-Written Code](https://arxiv.org/html/2603.06931v2)**
— Mikoláš Janota and Mirek Olšák, version 2; code at
[`MikolasJanota/llm2smt`](https://github.com/MikolasJanota/llm2smt).
The case study develops a QF_UF solver using an agent, with CaDiCaL handling SAT.
Human work includes prompts, fuzzing, delta debugging and proof examples.

Table 1 reports 7,468 solved instances, against 7,500 for Z3 and 7,494 for
cvc5. A “win” means within one second of the fastest solver; the corresponding
counts are 6,486, 7,491 and 7,308. It is not a count of sub-second runs.
Table 2 separately reports 285 certified proofs with preprocessing, among
4,169 certification outcomes, including failures, timeouts, heartbeat limits
and stack overflows. The 7,468 solved instances are not that experiment's
denominator. This supports trying agent-built solvers at a narrow scale;
it does not demonstrate reliable proof production across SMT.

**[Carlini's C compiler experiment](https://github.com/anthropics/claudes-c-compiler)**
— Nicholas Carlini; the repository links to his account of the
experiment. It describes an agent-written Rust C compiler with x86, ARM and
RISC-V backends, capable of compiling a booting Linux kernel. This is evidence
about code generation at scale, not solver correctness or proof production.

## Automated maintenance — the thesis is published, the hard part is not solved

**[Continuous Autonomous Refactoring: A Research Roadmap](https://arxiv.org/html/2609.01236)**
— Sun, Ståhl, Sandahl and Kessler, Linköping. Automated
maintenance as an agenda: refactoring as a continuous process in which agents
monitor and improve a codebase against an evolving specification. It proposes rather than
demonstrates, and the open problems it lists apply to solver maintenance — local
improvements damaging global properties, and test suites being a guardrail
rather than a proof of behaviour preservation.

**[CodeTaste: Can LLMs Generate Human-Level Code Refactorings?](https://arxiv.org/abs/2603.04177v2)**
— Thillen, Mündler, Raychev and Vechev, version 2. Its abstract
distinguishes implementing a detailed refactoring request from discovering
the structural changes human developers choose; agents do better at the former.

**[A Differential Fuzzing-Based Evaluation of Functional Equivalence in LLM-Generated Code Refactorings](https://arxiv.org/abs/2602.15761v1)**
— Dristi and Dwyer. The abstract reports 19–35% functionally
non-equivalent refactorings across its evaluated models and datasets, with
about 21% of those missed by the existing tests. These are study-specific
numbers, not estimates for cvc5. See also
**[Articulate but Wrong](https://arxiv.org/pdf/2605.21537)** on self-review
failures in modernization, and
**[Environment-in-the-Loop](https://arxiv.org/html/2602.09944v1)** on migration
agents that have to drive a build.

**What this list does not establish is effective solver maintenance.**
The cited studies do not demonstrate that automated refactoring preserves a
solver's answers while improving its proof coverage. That is the local
experiment the maintenance proposal still needs.

## Where a rewrite's proof comes from — the argument all three bets inherit

**This is the one piece of related work that is not merely adjacent: it is the
published stance the proof-first bet is arguing against**, and it constrains
the other two as much as it constrains that one.

**[Nötzli, Barbosa, Niemetz, Preiner, Reynolds, Barrett and Tinelli,
*Reconstructing Fine-Grained Proofs of Rewrites Using a Domain-Specific
Language*, FMCAD 2022](https://homepage.divms.uiowa.edu/~ajreynol/fmcad2022.pdf).**
RARE — *rewrites, automatically reconstructed* — and the design cvc5 still
runs on.

**Their stance, which is a deliberate architectural choice and not an
omission.** The paper's opening move is to refuse instrumentation: it proposes
*"an alternative approach that does not rely on instrumenting the original
rewriter. Instead, our approach treats the rewriter as a black box"*, because
*"instrumenting this code to additionally produce proofs makes it even more
complex and makes it harder to add new rewrite rules."* A post-processing
reconstructor then searches the rule database for something that explains what
the rewriter already did. **The rewriter stays free; the proof is recovered
afterwards.**

**What the authors report, and what it costs.** Fine-grained proofs were
reconstructed for 95% of rewrite steps on their industrial set and 92% on
SMT-LIB — but only 20% of industrial benchmarks (5 of 25) and 22% of SMT-LIB
proofs containing rewrite steps (5,945 of 26,418) were *fully* fine-grained,
because one unreconstructed step makes a whole proof coarse. Reported cost: a
3.14× slowdown. The paper is also explicit about why a budget is needed at all:
*"there is no guarantee that preconditions are simpler than the current
equality to be proved, and so there is no guarantee of termination in
general."*

**Four further limits the authors state**, each of which bounds any proposal
here: commutativity is not built into matching and must be expressed as extra
rules; some rewriting is not rules at all (arithmetic *"boils down to
normalizing polynomials"*); `define-rule*` fixed-point rules already trade
reconstruction completeness for speed; and not every rewrite is expressible —
the string rewriter is *"over 3,000 lines of C++ code and distinguishes over
200 different rewrite rules. Moreover, not all of those rules can be expressed
as a single rewrite rule in RARE."*

> **Provenance.** The PDF was not opened here. Every quotation and figure above
> is taken from dokimasia's record of the paper in
> [`docs/rare-correspondence.md`](https://github.com/ajreynol/dokimasia/blob/main/docs/rare-correspondence.md)
> and [`docs/issues.md`](https://github.com/ajreynol/dokimasia/blob/main/docs/issues.md)
> `i-4`. **Dokimasia is the authority on the RARE correspondence and on what
> cvc5's proof coverage is**; nothing here restates its analysis or competes
> with it.

### The third answer, and it is a branch rather than a paper

**[`ajreynol/cvc5` branch `rdbExec`](https://github.com/ajreynol/cvc5/tree/rdbExec),
read at `4585967004`, branched from cvc5 `5cc03f4b95`.** Exploratory work in a
personal fork: not a release, not a cvc5 position, and not a claim this
repository is making about cvc5's plans.

It takes neither the paper's route nor the proof-first one. **RARE rules marked
`:exec` are compiled into the rewriter**: `rewrite_db_exec_printer.cpp`
generates straight-line matching C++ from the rules, the rewriter applies them
as a last resort, and the proof step records which rule fired so the
post-processor applies it directly *"rather than searching"*. Six rules carry
`:exec` at that commit, and one of them replaces a hand-written case deleted
from `SequencesRewriter`.

**Why it belongs on a reading list about new solvers.** The FMCAD stance and
the proof-first stance share a premise — that you either instrument a rewriter
by hand or reconstruct afterwards. **Generating the rewriter from the rules is
a third option, and it is the cheapest of the three to try**, because it needs
no new language, no new kernel and no new solver. Its evidence is a prototype,
not a measurement: nothing here establishes that it scales past six rules,
and the branch itself keeps the search as a fallback and still reconstructs
rule conditions through it.

## Proof-first design — the crowded half

The checker and verified-solver literature includes tools with different guarantees:
IsaSAT, versat, `cake_lpr`, SMTCoq, `bv_decide`, lean-smt and Carcara.
**[Lean-SMT](https://arxiv.org/pdf/2505.15796)**
([Springer](https://link.springer.com/chapter/10.1007/978-3-031-98682-6_11))
reconstructs cvc5 proofs as Lean proofs.
**[PBLean](https://arxiv.org/html/2602.08692)** concerns pseudo-Boolean proof
certificates for Lean 4. These target different fragments and do not establish
that a new SMT producer will be easy to build.

## Questions this reading list leaves open

**Proof-first from line one, built by agents.** The list does not settle the
authoring cost or runtime cost of designing a solver around proof production.
That is an experiment to run, not a claim that no such work exists.

**Whether a new solver is needed to make rewrites carry their proofs.** The
`rdbExec` branch above is a working counter-example at prototype scale, inside
a solver nobody rewrote. Nothing on this list says how far that route goes —
what fraction of 321 RARE rules could carry `:exec`, what the rules that cannot
have in common, or what the generated matcher costs at runtime. **Until that is
known, the proof-first bet is arguing for a solver against an alternative that
needs none**, and no source here settles it.

**The spine transplant.** Keeping cvc5's core engine as a working artifact and
rebuilding the architecture around it. No source on this list establishes the
cost of that approach; it remains outside the three proposals compared here.
