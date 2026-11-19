# Related work

Public work bearing on the solver approaches in [`approaches.md`](approaches.md)
and the rewriter terms defined in the
[README](../README.md#three-approaches-to-rewriter-maintenance), arranged by
which approach it helps or hurts. This is a reading list, not a systematic
survey. Results below are the authors' reports, not runs here.

## Agent-built solvers

**[LLM2SMT: Building an SMT Solver with Zero Human-Written Code](https://arxiv.org/html/2603.06931v2)**
— Mikoláš Janota and Mirek Olšák, version 2; code at
[`MikolasJanota/llm2smt`](https://github.com/MikolasJanota/llm2smt).
The case study develops a QF_UF solver using an agent, with CaDiCaL handling
SAT. Human work includes prompts, fuzzing, delta debugging and proof examples.

Table 1 reports 7,468 solved instances, against 7,500 for Z3 and 7,494 for
cvc5. A “win” means within one second of the fastest solver; the corresponding
counts are 6,486, 7,491 and 7,308. Table 2 separately reports 285 certified
proofs with preprocessing, among 4,169 certification outcomes, including
failures, timeouts, heartbeat limits and stack overflows; the 7,468 solved
instances are not that experiment's denominator. This supports trying
agent-built solvers at a narrow scale. It does not demonstrate reliable proof
production across SMT.

**[Carlini's C compiler experiment](https://github.com/anthropics/claudes-c-compiler)**
— Nicholas Carlini; the repository links to his account of the experiment. It
describes an agent-written Rust C compiler with x86, ARM and RISC-V backends,
capable of compiling a booting Linux kernel. This is evidence about code
generation at scale, not about solver correctness or proof production.

## Agent-written ports

**A verified Lean port of cvc5's rewriter and preprocessor**, written by agents
under human supervision outside this repository, read at `5661141`. Its
theorems state that every rewrite, and the output of preprocessing, is
equivalent to its input under Logos's semantics. Its rule table is generated
from the CPC signature, and differential fuzzing against cvc5 tests which rules
it chooses. It reports porting Booleans, arithmetic, UF, bit-vectors, arrays,
datatypes, finite sets in cvc5's safe-mode fragment, part of strings, and
quantifiers as far as they are Boolean. It also reports finding cvc5 rewrites
whose proofs contain trust steps; those findings are its own, and
[Dokimasia](https://github.com/ajreynol/dokimasia) is the authority on cvc5's
proof coverage. This is the evidence for the proof-first port. It concerns
simplification, not search.

## The verified Rust port

**[Verus](https://github.com/verus-lang/verus)** verifies Rust programs
against specifications written in Rust, discharging obligations with an SMT
solver. Its authors describe it in *Verus: Verifying Rust Programs using
Linear Ghost Types* (OOPSLA 2023) and *Verus: A Practical Foundation for
Systems Verification* (SOSP 2024). These are evidence that Verus handles
systems code. Nothing here establishes that it can carry an SMT solver's theory
reasoning.

**Refactoring by agents** bears on the second phase of the Rust port, which
refactors a working port until it is verifiable.

- **[Continuous Autonomous Refactoring: A Research Roadmap](https://arxiv.org/html/2609.01236)**
  — Sun, Ståhl, Sandahl and Kessler. Refactoring as a continuous process in
  which agents improve a codebase against an evolving specification. It
  proposes rather than demonstrates. Its open problems include local
  improvements damaging global properties and test suites being a guardrail
  rather than a proof of behaviour preservation.
- **[CodeTaste: Can LLMs Generate Human-Level Code Refactorings?](https://arxiv.org/abs/2603.04177v2)**
  — Thillen, Mündler, Raychev and Vechev, version 2. Agents do better at
  implementing a detailed refactoring request than at discovering the
  structural changes human developers choose.
- **[A Differential Fuzzing-Based Evaluation of Functional Equivalence in LLM-Generated Code Refactorings](https://arxiv.org/abs/2602.15761v1)**
  — Dristi and Dwyer. The abstract reports 19–35% functionally non-equivalent
  refactorings across its evaluated models and datasets, with about 21% of
  those missed by the existing tests. These are study-specific numbers, not
  estimates for a solver.
- See also **[Articulate but Wrong](https://arxiv.org/pdf/2605.21537)** on
  self-review failures in modernization, and
  **[Environment-in-the-Loop](https://arxiv.org/html/2602.09944v1)** on
  migration agents that have to drive a build.

**What this list does not establish** is that an agent-written port to Rust
preserves a solver's answers, or that a later refactoring makes it verifiable.
Differential testing against cvc5, plus checking the port's CPC proofs, is the
safeguard the Rust port proposes for the first phase.

## Where a rewrite's proof comes from

**[Nötzli, Barbosa, Niemetz, Preiner, Reynolds, Barrett and Tinelli,
*Reconstructing Fine-Grained Proofs of Rewrites Using a Domain-Specific
Language*, FMCAD 2022](https://homepage.divms.uiowa.edu/~ajreynol/fmcad2022.pdf).**
RARE, and the **proof-reconstructing rewriter** design cvc5 still uses.

**Its stance is a deliberate architectural choice.** The paper declines to
instrument the rewriter, because *"instrumenting this code to additionally
produce proofs makes it even more complex and makes it harder to add new
rewrite rules"*. Instead it *"treats the rewriter as a black box"*, and a
post-processor searches the rule database for an explanation of what the
rewriter did.

**What the authors report.** Fine-grained proofs were reconstructed for 95% of
rewrite steps on their industrial set and 92% on SMT-LIB. Only 20% of
industrial benchmarks (5 of 25) and 22% of SMT-LIB proofs containing rewrite
steps (5,945 of 26,418) were *fully* fine-grained, because one unreconstructed
step makes a whole proof coarse. The reported cost is a 3.14× slowdown. The
paper also explains why a budget is needed: *"there is no guarantee that
preconditions are simpler than the current equality to be proved, and so there
is no guarantee of termination in general."*

**Four further limits it states**, each of which bounds any proposal here:
commutativity is not built into matching and needs extra rules; some
rewriting is not rules at all (arithmetic *"boils down to normalizing
polynomials"*); `define-rule*` fixed-point rules trade reconstruction
completeness for speed; and not every rewrite is expressible — the string
rewriter is *"over 3,000 lines of C++ code and distinguishes over 200 different
rewrite rules. Moreover, not all of those rules can be expressed as a single
rewrite rule in RARE."*

> **Provenance.** The PDF was not opened here. The quotations and figures were
> taken from dokimasia's record of the paper, read at dokimasia `f9a5bd7`.
> Its current [register](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)
> (`i-4`, read at `de0f1b9`) still carries the termination quotation and the
> 92–95% and 20–22% bands. The other figures rest on that earlier reading until
> somebody opens the paper.

### The hybrid in `rdbExec`

**[`ajreynol/cvc5` branch `rdbExec`](https://github.com/ajreynol/cvc5/tree/rdbExec),
read at `4585967004`, branched from cvc5 `5cc03f4b95`.** Exploratory work in a
personal fork: not a release, not a cvc5 position, and not a claim about cvc5's
plans.

RARE rules marked `:exec` are compiled into the rewriter.
`rewrite_db_exec_printer.cpp` generates matching C++ from them. Handwritten
theory rewriters remain, and generated rules run when a theory rewriter leaves
a term unchanged. The proof step records which rule fired, so the
post-processor applies it directly *"rather than searching"*. Six rules carry
`:exec` at that commit, and one replaces a hand-written case deleted from
`SequencesRewriter`.

**Dokimasia's reading**, in
[`dokimasia-D13`](https://github.com/ajreynol/dokimasia/blob/main/docs/discussion.md#d13--exec-narrows-i-4-and-it-is-not-the-e4-we-called-an-open-problem)
at dokimasia `1eeae9b`, confirms what `:exec` removes from reconstruction and
bounds it, as [`approaches.md`](approaches.md#rewriter-maintenance) records. It
distinguishes this from compiling the rule database into the *reconstructor*,
which it calls an open problem. It has not read or run the branch, and its
reading moves if the summary above is wrong. Its `i-4`, `i-17` and `R9`
register rows are current at `de0f1b9`; the page that held its rewriter
experiments was removed, so cite the topic rather than a page.

## Verified checkers and verified solvers

The checker and verified-solver literature includes tools with different
guarantees: IsaSAT and versat verify SAT search; `cake_lpr`, SMTCoq,
`bv_decide` and Logos verify a checker; lean-smt reconstructs proofs; Carcara
and Ethos are independent but unverified.
**[Lean-SMT](https://arxiv.org/pdf/2505.15796)**
([Springer](https://link.springer.com/chapter/10.1007/978-3-031-98682-6_11))
reconstructs cvc5 proofs as Lean proofs.
**[PBLean](https://arxiv.org/html/2602.08692)** concerns pseudo-Boolean proof
certificates for Lean 4. These target different fragments. IsaSAT and versat
show what verifying search costs, which bears on the Rust port. The checkers
show that verifying a checker and leaving search untrusted is practical, which
bears on the proof-first port.

## Questions this reading list leaves open

**Whether the proof-first port extends to search.** The Lean port shows that
agents can port simplification against a verified calculus. Nothing here shows
the cost of doing the same for CDCL(T), theory combination or conflict
explanation, or the runtime cost against cvc5.

**Whether Verus can verify a solver.** No source here establishes it for
theory reasoning, and none measures what refactoring towards verification does
to a solver's performance.

**How a generated rewriter compares with a proof-producing one.** The `rdbExec`
branch uses generation at prototype scale inside a solver nobody rewrote.
Nothing here says what fraction of the 321 RARE rules at cvc5 `aee8742404` could
carry `:exec`, what the remainder have in common, or what the generated matcher
costs at runtime.

**The spine transplant.** Keeping cvc5's core engine as a working artifact and
rebuilding the architecture around it. No source here establishes its cost; it
remains outside the approaches compared here.
