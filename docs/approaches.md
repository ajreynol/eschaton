# Competing approaches

The [README](../README.md) names the three solver approaches and defines the
shared rewriter terms **generated rewriter**, **proof-producing rewriter** and
**proof-reconstructing rewriter**. It is the authority for both. This page
develops the tradeoffs without restating the definitions. Public work for each
approach is in [`related-work.md`](related-work.md).

## Broader solver approaches

| | keeps from cvc5 | written by | where the guarantee lives | dies if |
| --- | --- | --- | --- | --- |
| Proof-first port | the proof calculus, and the C++ as the behaviour to match | agents under human supervision, in Lean | certificates in cvc5's calculus, checked or proved sound once against Logos; search untrusted | the approach does not extend past simplification, or runtime cost is prohibitive |
| Verified Rust port | the design, with the C++ as a general guide | agents under human supervision, in Rust | Verus proofs about the code itself | refactoring for Verus costs the performance, or the verified part stays small |
| Agent-built solver | nothing | agents | none, unless added | the hard parts are exactly the parts that do not automate |

**The proof-first port is the current preference.** It is the only approach
with evidence: a verified Lean port of cvc5's rewriter and preprocessor,
written by agents under human supervision outside this repository. No solver
has been built or compared here.

### Why a proof-producing solver is a basis for agents

An agent porting a proof-producing solver has three things that an agent
starting from nothing lacks:

- **A specification for each step.** CPC's rules state what every rewrite and
  inference may conclude. The agent ports the choice of rule, not the rule.
- **A verified checker for that specification.** Logos proves CPC's rules sound
  in Lean. A port can prove once that any sequence of accepted steps preserves
  meaning, so the agent's code need not be trusted.
- **A reference implementation.** cvc5's output allows differential testing of
  coverage and behaviour, which the theorem does not address.

In the rewriter built this way, an agent's error can lose a rewrite or differ
from cvc5, but cannot return a non-equivalent term. The trusted parts are the
calculus, the checker's specification and the translation into it, and the
agent does not maintain them. This answers the agent-built solver's weakness —
that nothing gives a reason to believe the output — within the limits of
Logos's correctness statement.

**What the evidence does not show.** Rewriting and preprocessing are
equivalence-preserving transformations of formulas. A solver also needs search,
conflict explanation, theory combination and model construction, where porting
is harder and runtime cost decides usefulness. The implementation has already
moved some steps from checked certificates to Lean proofs about faster code,
for speed. That is a small step towards the Rust port's approach.

### The real disagreement: certificates or code

**The proof-first port and the Rust port are the real disagreement.** Both
port cvc5 with agents, and both aim at verification. They disagree about what
to verify:

- The proof-first port verifies a **fixed calculus and its checker**, and leaves
  search untrusted. Its cost is producing or justifying a certificate for every
  step. Its benefit is that the trusted part does not grow with the solver.
- The Rust port verifies **the solver's own code**. Its cost is the proof
  burden on a large, performance-critical program, and the trusted base
  includes Verus and the SMT solver Verus uses. Its benefit is that no per-run
  certificate is needed, and it starts in a language suited to speed.

The verified SAT solvers IsaSAT and versat show the cost of the second route
for SAT alone; see [related work](related-work.md#verified-checkers-and-verified-solvers). The Rust
port's claim is that agents change that cost, because the refactoring that
verification demands is the kind of work agents are good at. That claim is
untested.

**The two could combine.** A Rust port that keeps cvc5's proof production
emits certificates that Ethos and Logos can check before any Verus proof
exists. Verification could then move into the code one component at a time.

### The agent-built solver

**Building from nothing is a bet about production cost, not correctness.** Its
weakness is that nothing in it produces a reason to believe the output. The
other two approaches answer that by starting from a solver with a proof
calculus. The LLM2SMT study reports competitive QF_UF solving with limited
certification; see the
[source summary](related-work.md#agent-built-solvers).

## Rewriter maintenance

The rewriter comparison is the first test of any of the approaches above, and
is independent of which one is chosen.

**What the built rewriter chose.** The verified Lean rewriter writes no rules.
It generates its matcher from the CPC signature, ports cvc5's theory
strategies as untrusted code, and proves once that any sequence of
checker-accepted steps is an equivalence. Its cost per rule is therefore no
proof work. Rules computed by normalisation (polynomial and ACI normalisation)
are computed by ported code and then validated or proved. This is a
generated rewriter whose results also carry proofs. It is not the elaboration
from rules written in the host language that the original proposal described.

**What a proof-reconstructing rewriter would have to show.** Proving the rules
sound is only part of the task. To guarantee a proof for every supported
rewrite, one must also show that the rules cover the handwritten code's
behaviour and that reconstruction succeeds within its budget, including the
conditions of conditional rules. A checked proof establishes the step it
justifies, not this global guarantee.

**What the `rdbExec` hybrid changes.** In the
[`rdbExec` branch](https://github.com/ajreynol/cvc5/tree/rdbExec), read at
`4585967004` from cvc5 `5cc03f4b95`, generated rules run when the theory
rewriter leaves a term unchanged, and the proof producer applies the recorded
rule directly. This removes one of the two things reconstruction recurses on —
the gap between a rule's instantiated right-hand side and the target. Rule
conditions are still reconstructed through the same bounded search. Dokimasia's
answer [`dokimasia-D13`](https://github.com/ajreynol/dokimasia/blob/main/docs/discussion.md#d13--exec-narrows-i-4-and-it-is-not-the-e4-we-called-an-open-problem),
read at dokimasia `1eeae9b`, confirms this and bounds it.
[`i-4`](https://github.com/ajreynol/dokimasia/blob/main/docs/README.md#the-register)
is a claim about the procedure, so its termination status is unchanged. The
budget is spent per reconstruction, so six compiled rules are *"a smaller
constant"*, which *"is not an argument"*. For a compiled rule, the RARE rule
and the generated C++ are no longer two statements of one fact, at the cost of
recording the step as trusted and reconstructing it later.

**What would move the comparison.** It needs a measurement, not another
argument. How many of cvc5's RARE rules could carry `:exec` — there were 321 at
cvc5 `aee8742404` — and what do the ones that cannot have in common? On the
other side, what does the Lean rewriter cost at runtime against cvc5 on the same
terms? **An experiment that beats the 2022 reconstruction paper but loses to
the hybrid has not settled anything in the proof-first port's favour.**

## How any of this gets decided

Each approach has an experiment that could kill it:

- **Proof-first port:** extend the Lean port to one piece of search — for
  example, a CDCL(T) core for one theory — and measure whether certificates or
  Lean proofs keep pace with the port.
- **Verified Rust port:** port one cvc5 subsystem to Rust, refactor it until
  Verus verifies a meaningful property, and measure the cost and the
  performance relative to the C++.
- **Agent-built solver:** a new theory solver that passes somebody else's
  benchmark set with checkable output.

Only the first has started, and only for simplification. The preference
remains provisional until these experiments produce evidence.
