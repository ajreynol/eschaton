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

All child projects are unadvertised. Keep this declaration, with a reason,
in each child's README introduction:

```markdown
**Footing:** `unadvertised-child` — speculative research with no implementation.
```

Keep their names and inward links out of the parent README, documentation
index, reports and other reader-facing documentation.

Read correspondence freely. Work a topic only under the human instruction
required by [the discussion gate](discussion.md), and only when its `To:` field
names eschaton. Draft replies here for a person to carry; write no other tree.
When a topic ends, remove it after placing any lasting decision in the document
it governs. Allocate new ids above every id in this file and its Git history.

## Repository checks

[Anoieu's policy checker](https://github.com/ajreynol/anoieu) checks the
maintenance declaration, documentation index, discussion preamble, links and
other repository conventions. It does not establish that the research claims
are true. There are no `.eo` or `.eos` inputs here for its analyzer yet.

With an Anoieu checkout beside this repository, run from eschaton's root:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 ../anoieu/scripts/policy_check.py --policy-version 1 --root .
```

This uses the sibling checkout's version. To
reproduce CI, use an Anoieu checkout at the full `ANOIEU_REV` commit in
[the workflow](../.github/workflows/anoieu.yml). The workflow runs the same
checker on pushes and pull requests with Python 3.12; the checker requires
Python 3.10 or later and no third-party packages.

Use a separate checkout under ignored `scratch/` when the sibling is at another
revision; do not reset a shared checkout. The workflow is the source of truth
for the pin, contract and invocation. No local comparison checks this prose
against its YAML. These are the whole CI suite here: there is no solver test
suite. A pass establishes repository conventions, not research results.

The governing policy reference is kanon
`dc6f56942fbc567abea76c562565557e5e7c6e19`; it is separate from the checker pin.

## Updating the checker

Choose an Anoieu commit whose own CI passed, update `ANOIEU_REV` in the workflow,
and run that version against this tree before landing the change. Inspect
Anoieu's `ci` workflow result for that exact full commit, including its required
jobs; a green branch tip at another commit is not evidence. This remote check
belongs in the update process, outside CI. If the result is absent, unfinished
or failing, keep the existing pin. The pinned commit has a successful
[CI run](https://github.com/ajreynol/anoieu/actions/runs/35270952777).

Anoieu provides a
[stable checker contract and shared workflow](https://github.com/ajreynol/anoieu/blob/154228a40d21584b95f4029742ccc8f432ea87f5/docs/policy-checker.md).
This job explicitly selects contract 1 and keeps its implementation pinned
while kanon's adoption policy requires it. Following the shared workflow at
`main` also requires the governing adoption instructions to permit that choice;
the pending request and reply are in [discussion](discussion.md).
