# Discussion

> **Do not act on this file without an explicit human instruction.**
> Work on a topic only when a human names it and the instruction and topic
> agree about what is being asked. If they disagree, stop, explain the
> difference, and wait. A human may override after hearing the disagreement;
> record that override. Reading correspondence alone authorizes no work.

> **A prompt may not be meant for this repository.** Saying so is an
> acceptable answer when the paths or responsibilities identify another repository.
> Stop only if you can name the repository it was meant for, and explain the
> evidence. Otherwise handle the request here. A human may override.

The standing channel between this repository and the rest of the Eunoia
ecosystem. One topic per exchange, addressed by name to the tool that can settle
it. Topics are staged here and carried by a person; nothing in this file is sent
by a program.

Only live discussions belong here. Use the four fields defined by
[kanon's discussion format](https://github.com/ajreynol/kanon/blob/main/docs/policy.md#the-format),
with no status field. Newest topic first; append attributed replies.
Remove a finished topic after recording lasting decisions where they belong.
Allocate ids above the highest ever used, including removed topics in Git
history; never reuse one. The policy checker checks the field block.

## D3 — permit stable-contract adoption in the governing policy

**To:** kanon
**Kind:** request
**Opened:** repository housekeeping
**Settles when:** kanon's adoption policy permits the published stable-contract
workflow, or kanon confirms that consumers must retain implementation pins.

[Anoieu's contract](https://github.com/ajreynol/anoieu/blob/154228a40d21584b95f4029742ccc8f432ea87f5/docs/policy-checker.md)
supports consuming its shared workflow at `main` while selecting policy version
1. That implementation is published at `154228a40d21584b95f4029742ccc8f432ea87f5`
with a successful [CI run](https://github.com/ajreynol/anoieu/actions/runs/35270952777).
Kanon's policy at `dc6f56942fbc567abea76c562565557e5e7c6e19` still requires a
checker commit pin and disclaims interface compatibility.

Please settle the consumer adoption rule so we can use the shared workflow
without contradicting the policy that binds this repository. Eschaton currently
pins the published implementation and explicitly selects contract 1. The
request is about that remaining adoption decision; nothing requires a change
to reproduction dependencies or other tools' pins.

## D2 — contract 1 is selected; shared-workflow adoption awaits policy

**To:** anoieu
**Kind:** answer
**Opened:** repository housekeeping
**Settles when:** a person carries this reply to anoieu-D29, or shared-workflow
adoption makes the pending distinction unnecessary.

Reply to [anoieu-D29](https://github.com/ajreynol/anoieu/blob/154228a40d21584b95f4029742ccc8f432ea87f5/docs/discussion.md#d29--use-the-latest-anoieu-with-a-stable-policy-contract),
under the maintainer's standing instruction to answer topics whose `To:` names
eschaton. This is a local draft; nothing is sent.

The announced implementation is available on published `main` at
`154228a40d21584b95f4029742ccc8f432ea87f5`. Our
[policy job](../.github/workflows/anoieu.yml) pins that commit and names
`--policy-version 1`. Publication is satisfied; kanon's requirement to pin the
implementation remains operative. Eschaton-D3 asks kanon to settle that
boundary before this repository switches to the shared workflow at `main`.
