# Discussion

The standing channel between this repository and the rest of the Eunoia
ecosystem. One topic per exchange, addressed by name to the tool that can settle
it. Topics are staged here and carried by a person; nothing in this file is sent
by a program.

Every topic carries five fields. **To** names the tool that can settle it.
**Kind** is one of request, proposal, question, notice or answer. **Status** is
one of open, answered, declined, withdrawn or settled. **Opened** is the date it
was written. **Settles when** says what would end it, so that a topic nobody has
answered can still be closed by a fact. Ids are allocated once, in order, and
are never reused. Newest topic first.

*This file is provisional: it carries one topic and none of the shared preamble
the ecosystem's policy asks of it. `join_eo` supplies the rest when this
repository joins.*

## D1 — the register has no row for `eschaton`, and none for `telos`

**To:** kanon
**Kind:** request
**Status:** open
**Opened:** 2026-09-16
**Settles when:** kanon adds rows for both names to `tools/ynoia/names.md`, or
declines and says which name it would rather we used.

`tools/ynoia/names.md` at kanon `e65250ea` ("Initial corrections",
2026-09-15) contains no entry for `eschaton` in any of its three tables, and the
string does not occur anywhere else in the ecosystem. The register's own closing
rule is that a new repository does not edit that file, so this is a request
rather than a patch.

**We did not wait for it.** The README here was written and the etymology stated
without a row to take it from, which inverts the intended order — the register
is meant to carry the etymology and a line of scope, and the repository's README
is meant to explain a name somebody already wrote down. If kanon reads the
etymology and wants it different, or wants a different name, the README follows
the register and not the other way round.

**Second gap, same page.** `telos` has no row either, though it has been a child
project in `dokimasia` for some time and is used in kanon's own `actionable.md`,
`handover.md` and `scripts/ecosystem/ecosystem.json`. By the register's own
taxonomy that is an *In use elsewhere, and claimed by nobody* row that was never
written — the failure mode that section exists to catch.

The version of the register these claims were read from is kept verbatim in
`ynoia-brief.local.md`, deliberately out of tree, so the reading can be checked
against what the page said on the day rather than against what it says now.
