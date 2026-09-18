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
`ad18fb2108f9560ca8327de9b86e064b15287e4b`; it is separate from the checker pin.

## Which form of the check this repository runs

Anoieu offers two forms and
[the governing policy](https://github.com/ajreynol/kanon/blob/main/docs/policy.md#2-run-the-check)
accepts either: a pinned checker commit, or a call to anoieu's
[shared workflow](https://github.com/ajreynol/anoieu/blob/main/docs/policy-checker.md)
at `main` naming a policy contract. **This repository pins the implementation
and names contract 1**, which is a decision rather than a default.

The pin keeps the checker implementation fixed until a commit here changes it.
Its policy verdict can be reproduced offline with the same input tree, checker
revision and a compatible Python interpreter. This matters in a repository
whose entire CI is this one job and whose content is prose. The hosted job still
depends on GitHub, the network, runner images and action versions; pinning the
checker does not make those dependencies reproducible or prevent their failures.

The shared-workflow form fixes the obligations through a contract and lets the
implementation move. A rerun can therefore report a different policy verdict
after an implementation update, including a fix for a missed violation. Contract
1 permits fixes, not new requirements; it does not guarantee that the checker
has no bugs. Either form's checker can be run locally, but a called GitHub
workflow itself requires a hosted run.

## Updating the checker

Choose an Anoieu commit whose own CI passed, update `ANOIEU_REV` in the workflow,
and run that version against this tree before landing the change. Inspect
Anoieu's `ci` workflow result for that exact full commit, including its required
jobs; a green branch tip at another commit is not evidence. This remote check
belongs in the update process, outside CI. If the result is absent, unfinished
or failing, keep the existing pin. The pinned commit has a successful
[CI run](https://github.com/ajreynol/anoieu/actions/runs/35270952777).
