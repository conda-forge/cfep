
<table>
<tr><td> Title </td><td> Feedstock Statuses </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Christopher J. "CJ" Wright &lt;cjwright4242@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sept 29, 2019</td></tr>
<tr><td> Discussion </td><td> <a href="https://github.com/conda-forge/cfep/pull/15">cfep#15</a> </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This document proposes the creation of four new statuses for feedstocks that denote the
standing of the feedstock and transistions between the standings.
These statuses will help us triage feedstocks and identify which feedstocks need
help, maintainers, or archiving.

## Motivation

It can be difficult for maintainers and would-be maintainers to identify which feedstocks could use their
help.
Additionally, it is difficult for core to know which feedstocks need additional help or archiving.
Finally, users of CF packages may find it difficult to understand which packages are dormant.
This proposal aims to expose this information more clearly.

## Definitions

This proposal creates four new statuses:

### In Excellent Standing
The feedstock is actively maintained. New versions of the packages are being created as needed, PRs and issues are addressed
promptly. Fewer than 3 bot PRs are open at once.

### In Good Standing
Maintainers are responsive but probably overworked. Multiple PRs and issues have begun to pile up.

### Unsupported
This feedstock has no or unresponsive maintainers.

Such a feedstock either needs new maintainers or to be archived. Set a deadline of X days/weeks/months for new
maintainers to come on board or existing maintainers to re-engage. If nothing happens the feedstock is archived.

### Archived
Effectively removed from the CF ecosystem.

Keep a public list of these so that would be maintainers can see where they can help out and so users
can understand if new packages will be created.

## Policies

### Maintainer Interactions

- For feedstocks in excellent standing, additional maintainers are always
  welcome but not actively recruited.
- Feedstocks in good standing should be advertised as needing additional
  maintainers.
- The maintainers of unsupported feedstocks should be encouraged to either
  re-engage with the feedstock or remove themselves. New maintainers should be
  actively recruited.
- Potential maintainers interested in resurrecting archived feedstocks can
  ping core in conda-forge.github.io.

## Implementation Discussion
- Where should we put the status information?
- Should we produce bots which update the status?
- What is the best way to engage maintainers and users to help support unsupported feedstocks?

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
