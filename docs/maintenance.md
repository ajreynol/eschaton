# Maintaining eschaton

Start with the [README](../README.md), then read kanon's shared
[policy](https://github.com/ajreynol/kanon/blob/main/docs/policy.md) and
[vision](https://github.com/ajreynol/kanon/blob/main/docs/vision.md).
This repository compares solver designs; it ships no solver or analyzer.
Describe the present state and distinguish proposals from measured results.
Keep source revisions with measurements so their scope remains explicit.
The maintainer requests documentation without calendar dates; discussion
`Opened` fields identify the work context instead.
List new documents in [the index](README.md). Use ignored `scratch/` and
`*.local.md` files for local working material.

The maintainer requests that all child projects remain unadvertised. Each
child's README declares the `unadvertised-child` footing with the reason,
which kanon's status audit reads to omit it from the default listing.
`--all-children` still includes it and explains the declaration. The parent
README compares approaches without naming or linking the child projects; keep
their names and links out of the parent's documentation index and reports too.
They remain research notes, isolated from the parent's code and CI; any
implementation lives in its own repository. The rewriter terms name shared techniques rather than child
projects.

Correspondence follows
[kanon's discussion-file rules](https://github.com/ajreynol/kanon/blob/main/docs/policy.md#the-discussion-file),
which this guide does not restate. What is local to this repository: work a
topic only when its `To:` field names eschaton; draft every reply here for a
person to carry and write no other tree; and put a lasting decision in the
document it governs rather than leaving it in a topic — which form of the check
this repository runs is recorded below for that reason. Ids are allocated above
every id in [`discussion.md`](discussion.md) and its Git history, which is where
a removed topic's number survives.

## Repository checks

[Anoieu's policy checker](https://github.com/ajreynol/anoieu) checks the
maintenance declaration, documentation index, discussion preamble, links and
other repository conventions. It does not establish that the research claims
are true. There are no `.eo` or `.eos` inputs here for its analyzer yet.

With an Anoieu checkout beside this repository, run from eschaton's root:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 ../anoieu/scripts/policy_check.py --policy-version 1 --root .
```

This uses the sibling checkout's version. CI uses anoieu's current `main`, and
each checker run logs its full implementation commit and selected contract. To
reproduce a particular CI verdict, use that logged commit with the same input
tree and contract. The [workflow](../.github/workflows/anoieu.yml) runs on pushes
and pull requests through anoieu's shared workflow, which uses Python 3.12; the
checker requires Python 3.10 or later and no third-party packages.

Use a separate checkout under ignored `scratch/` when the sibling is at another
revision; do not reset a shared checkout. The workflow is the source of truth
for the contract and invocation. No local comparison checks this prose
against its YAML. These are the whole CI suite here: there is no solver test
suite. A pass establishes repository conventions, not research results.

## Which form of the check this repository runs

**This repository follows anoieu's `main` and selects policy contract 1** through
the
[shared workflow](https://github.com/ajreynol/anoieu/blob/main/policy_check/README.md)
recommended by
[kanon's policy](https://github.com/ajreynol/kanon/blob/main/docs/policy.md#2-run-the-check).
This receives implementation fixes automatically and removes manual checker-pin
updates. For this repository's policy checks, those benefits take priority over
keeping the implementation fixed between runs.

Contract 1 fixes the automated requirements, applicability and severity. A rerun
can report a different verdict after an implementation update, including a fix
for a missed violation or a regression. The logged checker commit identifies
what ran. The contract does not version kanon's policy document or guarantee
that the checker has no bugs.

Changing the contract is a local decision: review anoieu's contract documentation,
run the selected contract against this tree, and update the workflow and this
guide together. The checker can run locally; the called GitHub workflow itself
requires a hosted run. GitHub may display the check as `anoieu / policy / policy`.
