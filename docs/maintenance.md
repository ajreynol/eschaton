# Maintaining eschaton

Keep research claims dated and distinguish proposals from measured results.
List new documents in [the index](README.md). Use ignored `scratch/` and
`*.local.md` files for local working material.

All child projects are unadvertised. Keep the standalone declaration
`**Eunoia listing:** unadvertised` in each child's README introduction, before
the first section heading. Keep their names and inward links out of the parent
README, documentation index, reports and other reader-facing documentation.

## Repository checks

[Anoieu's policy checker](https://github.com/ajreynol/anoieu) checks the
maintenance declaration, documentation index, discussion preamble, links and
other repository conventions. It does not establish that the research claims
are true. There are no `.eo` or `.eos` inputs here for its analyzer yet.

With an Anoieu checkout beside this repository, run from eschaton's root:

```bash
python3 ../anoieu/scripts/policy_check.py --root .
```

This uses the sibling checkout's version, as Kanon's `status_eo` does. To
reproduce CI, use an Anoieu checkout at the full `ANOIEU_REV` commit in
[the workflow](../.github/workflows/anoieu.yml). The workflow runs the same
checker on pushes and pull requests with Python 3.12; the checker requires
Python 3.10 or later and no third-party packages.

## Updating the checker

Choose an Anoieu commit whose own CI passed, update `ANOIEU_REV` in the workflow,
and run that version against this tree before landing the change. With Kanon
checked out alongside this repository, verify the proposed pin with:

```bash
python3 ../kanon/scripts/bump_check.py --root .
```

This reads GitHub and belongs in the update process, outside CI. If it cannot
verify a passing result, keep the existing pin. The initial pin's seven checks
passed in [this run](https://github.com/ajreynol/anoieu/actions/runs/35027348919).
