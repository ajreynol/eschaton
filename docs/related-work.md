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

**The spine transplant.** Keeping cvc5's core engine as a working artifact and
rebuilding the architecture around it. No source on this list establishes the
cost of that approach; it remains outside the three proposals compared here.
