
<table>
<tr><td> Title </td><td> Feedstock Statuses </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Christopher J. "CJ" Wright &lt;cjwright4242@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sept 29, 2019</td></tr>
<tr><td> Discussion </td><td> link to the PR where the CFEP is being discussed, NA is circulated initially </td></tr>
<tr><td> Implementation </td><td> link to the PR for the implementation, NA if not availble </td></tr>
</table>

## Abstract

This document proposes the creation of four new statuses for feedstocks that denote the	
standing of the feedstock and transistions between the standings.
These statuses will help us triage feedstocks and identify which feedstocks need
help, maintainers, or archiving.

## Motivation

It can be difficult for maintainers and would be maintainers to identify which feedstocks could use their
help.
Additionally, it is difficult for core to know which feedstocks need additional help or archiving.
Finally, users of CF packages may find it difficult to understand which packages are dorment.
This proposal aims to expose this information more clearly.

## Specification

This proposal creates four new statuses

### In Perfect Standing
The feedstock is actively maintained, new versions of the packages are being created as needed, autotick bot PRs are addressed 
(merged, fixed, or closed) promptly. Less than 3 bot PRs are open at once.
More maintainers are always welcome.

### In Good Standing
Maintainers are responsive but probably overworked. Multiple bot PRs have begun to pile up.
We should advertise these as needing new maintainers.

### Unsupported
This feedstock has no or unresponsive maintainers and either needs new maintainers or to be archived.
Advertise that this needs maintainers and for the current ones re-engage with the feedstock or remove
themselves, otherwise the feedstock will be archived. Set a deadline of X days/weeks/months for new
maintainers to come on board or existing maintainers to re-engage, if nothing happens the feedstock is archived.

### Archived
Effectively removed from the CF ecosystem. 
Keep a public list of these so that would be maintainers can see where they can help out and so users
can understand if new packages will be created.
New maintainers can ping core in conda-forge.github.io to ask for an archived feedstock be reactivated.


## Implementation Discussion
- Where should we put the status information?
- Should we produce bots which update the status?
- What is the best way to engage maintainers and users to help support unsupported feedstocks?

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
