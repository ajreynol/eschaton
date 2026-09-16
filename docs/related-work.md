# Related work

Public work that bears on the three bets in [`approaches.md`](approaches.md),
arranged by which bet it helps or hurts. **Searched 2026-09-16, and a first pass
rather than a survey** — everything here was found in an afternoon, so absence
from this page is weak evidence of absence.

## hawkeye — already done once, at the easy end

**[LLM2SMT: Building an SMT Solver with Zero Human-Written Code](https://arxiv.org/abs/2603.06931)**
— Mikoláš Janota and Mirek Olšák, arXiv:2603.06931, March 2026. Code at
[`MikolasJanota/llm2smt`](https://github.com/MikolasJanota/llm2smt). This is
hawkeye's bet, run: an LLM coding agent builds a DPLL(T) solver for QF_UF —
Nieuwenhuis–Oliveras congruence closure, CaDiCaL 3.x through IPASIR-UP — with no
human-written code.

It largely worked. 7,468 of 7,500 SMT-LIB instances solved, against Z3's 7,500
and cvc5's 7,494, though the win count within one second (6,486 against 7,491
and 7,308) is where the gap shows. The authors' own verdict is a "qualified yes",
and the caveat is the useful part: *zero human-written code* did not mean zero
human work. It took systematic fuzzing, delta-debugging infrastructure, explicit
resource limits, prompts steering the agent onto CaDiCaL, and worked example
proofs. The first solver the agent produced did not handle Boolean connectives
at all, and the preprocessor had to be told that `t = t` simplifies. They call
the pattern *jagged intelligence* — hard algorithms right, trivial things wrong.

**The part hawkeye should read twice is the proofs.** The solver emits Lean
proofs for unsat instances, and proof generation is named as the hardest thing
to get out of the agent, needing significant human guidance. Only 285 of 7,468
instances ended up with a certified proof; the rest failed on `grind`, timed out,
or hit Lean's heartbeat limit. No erroneous proof was found. So the one
component telos insists must come first is the one that came last here and went
worst — which is either a coincidence or the whole argument.

**[Building a C compiler with a team of parallel Claudes](https://www.anthropic.com/engineering/building-c-compiler)**
— Nicholas Carlini, Anthropic, February 2026. Sixteen agents, ~2,000 sessions
and about $20,000 of API time produced a 100,000-line Rust C compiler that
builds Linux 6.9 on x86, ARM and RISC-V, coordinating through git without
real-time human review. Evidence about *scale*, not about reasoning tools: it
says a project of solver size is not off the table.

## cvc6 — the thesis is published; the hard part is unsolved

**[Continuous Autonomous Refactoring: A Research Roadmap](https://arxiv.org/html/2609.01236)**
— Sun, Ståhl, Sandahl and Kessler, Linköping, September 2026. This is cvc6's
premise stated as an agenda: refactoring as a continuous process where agents
monitor and improve a codebase against an evolving specification, rather than as
occasional manual intervention. It is a roadmap, so it proposes rather than
demonstrates, and the open problems it lists are the ones cvc6 inherits — that
local improvements can damage global properties, and that existing test suites
are a guardrail and not a proof of behaviour preservation.

**[CodeTaste: Can LLMs Generate Human-Level Code Refactorings?](https://arxiv.org/pdf/2603.04177)**
— Thillen, Mündler, Raychev and Vechev, 2026. Frames the gap cvc6 lives in:
agents produce functional patches well and comprehensible, extensible structure
badly.

**[A Differential Fuzzing-Based Evaluation of Functional Equivalence in LLM-Generated Code Refactorings](https://arxiv.org/pdf/2602.15761)**
— Dristi and Dwyer, February 2026. Asks cvc6's real question — did the refactor
preserve behaviour? — on Python. *We did not extract its numbers; the PDF read
returned metadata only. Somebody should read it properly before it is cited for
a result.* See also **[Articulate but Wrong: Self-Review Failures in LLM-Based
Code Modernization](https://arxiv.org/pdf/2605.21537)** and
**[Environment-in-the-Loop](https://arxiv.org/html/2602.09944v1)** on migration
agents that must drive a build environment.

**What is missing is the part cvc6 is about.** None of this is a C++ codebase of
cvc5's size — dokimasia measures 521,073 lines in `src/` — and none of it treats
behaviour preservation for a *solver*, where the specification is a logic and a
wrong answer can be silent.

## telos — the crowded half

The verified-checker side is the one with existing tools, and telos already
tabulates them with what each guarantee actually is, in
[`kernel-of-cvc5.md`](../tools/telos/docs/kernel-of-cvc5.md#prior-art-and-what-each-guarantee-actually-is):
IsaSAT, versat, `cake_lpr`, SMTCoq, `bv_decide`, lean-smt and Carcara. Not
repeated here. Two additions found in this pass:
**[Lean-SMT](https://arxiv.org/pdf/2505.15796)** (Springer version
[here](https://link.springer.com/chapter/10.1007/978-3-031-98682-6_11)), which
replays cvc5 proofs in Lean and is the flexible counterpart to SMTCoq's rigid
full verification, and **[PBLean](https://arxiv.org/html/2602.08692)**,
pseudo-Boolean proof certificates for Lean 4.

## What nobody appears to be doing

Two gaps, stated so they can be disproved cheaply.

**Proof-first from line one, built by agents.** LLM2SMT is the near miss: the
solver came first and the proofs were bolted on, which is exactly the build
order telos says produced cvc5's holes, reproduced in eight months instead of
twenty years.

**The spine transplant** — keeping cvc5's core engine as a working artifact and
rebuilding the architecture around it. Unclaimed here and unfound out there.
