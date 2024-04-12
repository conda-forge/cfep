
<table>
<tr><td> Title </td><td> TB2C - Too Big To Be Compromised </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Matthew R. Becker &lt;becker.mr@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> April 12, 2024</td></tr>
<tr><td> Updated </td><td> April 12, 2024</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

There are some feedstocks on `conda-forge` that are "too big to be compromised" (TB2C).  These feedstocks
form core parts of the ecosystem and a malicious build by a malicious maintainer could have dire consequences
for our users and the reputation of `conda-forge`.  This CFEP proposes a new policy for these feedstocks to provide
additional controls on who can build and upload packages from them.

## Specification

The `conda-forge/core` team would maintain a list of TB2C feedstocks in a central, publicly accessible location.

The following rules would be applied to these feedstocks and this list:

- Only members of the `conda-forge/core` team, upstream maintainers of the software in the feedstock, or maintainers
  approved by the `conda-forge/core` team (either through unanimous agreement of the existing `conda-forge/core` maintainers on
  the feedstock or with a 50% sensitive vote of the full `conda-forge/core` team) would be allowed access to these feedstocks.
- Any non-core maintainer can be removed from the feedstock by a 50% sensitive vote of the `conda-forge/core` team.
- Code reviews are required for all PRs to these feedstocks.
- All automated merges must be initiated directly by a human (e.g., a maintainer adds a label). The `conda-forge` admin migrations
  bot is exempt from this rule.
- No uploads from outside of the CI (i.e., CFEP-03) are allowed.
- The full dependency tree of all feedstocks shipping packages the feedstock in question uses in its build is
  also required to be in the TB2C list.
- The `conda-forge/core` team can, at its discretion, add or remove feedstocks to/from TB2C list, either through consensus
  or a 50% public vote if no consensus can be reached.
- Anyone can petition the `conda-forge/core` team to add or remove a feedstock to/from the TB2C list through a 50% public vote.
- A single vote to add a feedstock to the TB2C list can be used to both add the feedstock and any non-core, non-upstream maintainers
  since both votes use the same threshold. In this case, the vote would be sensitive as opposed to public.

## Rationale

The goal of the rules above is to limit commit access to the feedstock to a small group of "trusted" individuals and to force
all builds of a feedstock to be completely open. These steps won't provide perfect security, but they will make it harder for a
malicious actor to compromise a feedstock.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
