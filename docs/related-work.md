# Related work

Public work bearing on the three bets in [`approaches.md`](approaches.md),
arranged by which one it helps or hurts. Searched 2026-09-16 and a first pass
rather than a survey, so absence from this page is weak evidence of absence.

## Agent-built solvers — already done once, at the easy end

**[LLM2SMT: Building an SMT Solver with Zero Human-Written Code](https://arxiv.org/abs/2603.06931)**
— Mikoláš Janota and Mirek Olšák, arXiv:2603.06931, March 2026; code at
[`MikolasJanota/llm2smt`](https://github.com/MikolasJanota/llm2smt). An LLM
coding agent builds a DPLL(T) solver for QF_UF —
Nieuwenhuis–Oliveras congruence closure, CaDiCaL 3.x through IPASIR-UP — with no
human-written code.

It largely worked: 7,468 of 7,500 SMT-LIB instances solved, against Z3's 7,500
and cvc5's 7,494, with the gap showing in the sub-second win count — 6,486
against 7,491 and 7,308. The caveat is the useful part. *Zero human-written
code* did not mean zero human work: it took systematic fuzzing, delta-debugging
infrastructure, explicit resource limits, prompts steering the agent onto
CaDiCaL, and worked example proofs. The first solver the agent produced did not
handle Boolean connectives at all, and the preprocessor had to be told that
`t = t` simplifies. The authors call the pattern *jagged intelligence*, and
their verdict is a qualified yes.

**The proofs are the part to read twice.** The solver emits Lean proofs for
unsat instances, and proof generation is named the hardest thing to get out of
the agent. Only 285 of 7,468 instances ended with a certified proof; the rest
failed on `grind`, timed out, or hit Lean's heartbeat limit. No erroneous proof
was found. The component proof-first design puts first is the one that came last
here and went worst.

**[Carlini's C compiler experiment](https://github.com/anthropics/claudes-c-compiler)**
— Nicholas Carlini, February 2026; the repository links to his account of the
experiment. Sixteen agents, about 2,000
sessions and $20,000 produced a 100,000-line Rust C compiler that builds Linux
6.9 on x86, ARM and RISC-V, coordinating through git without real-time human
review. Evidence about scale rather than about reasoning tools: a project of
solver size is not off the table.

## Automated maintenance — the thesis is published, the hard part is not solved

**[Continuous Autonomous Refactoring: A Research Roadmap](https://arxiv.org/html/2609.01236)**
— Sun, Ståhl, Sandahl and Kessler, Linköping, September 2026. Automated
maintenance as an agenda: refactoring as a continuous process in which agents
monitor and improve a codebase against an evolving specification. It proposes rather than
demonstrates, and the open problems it lists apply to solver maintenance — local
improvements damaging global properties, and test suites being a guardrail
rather than a proof of behaviour preservation.

**[CodeTaste: Can LLMs Generate Human-Level Code Refactorings?](https://arxiv.org/pdf/2603.04177)**
— Thillen, Mündler, Raychev and Vechev, 2026. A gap in automated maintenance: agents
produce functional patches well and comprehensible, extensible structure badly.

**[A Differential Fuzzing-Based Evaluation of Functional Equivalence in LLM-Generated Code Refactorings](https://arxiv.org/pdf/2602.15761)**
— Dristi and Dwyer, February 2026. The central maintenance question — did the
refactor preserve behaviour? — asked on Python. *Numbers not extracted; the PDF read
returned metadata only, so read it before citing it for a result.* See also
**[Articulate but Wrong](https://arxiv.org/pdf/2605.21537)** on self-review
failures in modernization, and
**[Environment-in-the-Loop](https://arxiv.org/html/2602.09944v1)** on migration
agents that have to drive a build.

**What is missing is evidence about solver maintenance.** None of it is a C++
base of cvc5's size — 521,073 lines in `src/` — and none of it treats behaviour
preservation for a *solver*, where the specification is a logic and a wrong
answer is silent.

## Proof-first design — the crowded half

The verified-checker side already has tools with differing guarantees:
IsaSAT, versat, `cake_lpr`, SMTCoq, `bv_decide`, lean-smt and Carcara. Two
additions from this pass: **[Lean-SMT](https://arxiv.org/pdf/2505.15796)**
([Springer](https://link.springer.com/chapter/10.1007/978-3-031-98682-6_11)),
which replays cvc5 proofs in Lean and is the flexible counterpart to SMTCoq's
rigid full verification, and **[PBLean](https://arxiv.org/html/2602.08692)**,
pseudo-Boolean proof certificates for Lean 4.

## What nobody appears to be doing

**Proof-first from line one, built by agents.** LLM2SMT is the near miss: the
solver came first and the proofs were bolted on afterwards — the build order
that proof-first design questions, arrived at again by a project with no legacy
to blame it on.

**The spine transplant.** Keeping cvc5's core engine as a working artifact and
rebuilding the architecture around it. Unclaimed here, and unfound out there.
