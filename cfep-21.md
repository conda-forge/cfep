
<table>
<tr><td> Title </td><td> Conda Forge-Bioconda Transfer Policy </td>
<tr><td> Status </td><td> Draft | Proposed | Accepted | Rejected | Deferred | Implemented </td></tr>
<tr><td> Author(s) </td><td> Mervin Fansler &lt;fanslerm@mskcc.org&gt;</td></tr>
<tr><td> Created </td><td> Jan 03, 2024</td></tr>
<tr><td> Updated </td><td> Jan 03, 2024</td></tr>
<tr><td> Discussion </td><td> link to the PR where the CFEP is being discussed, NA is circulated initially </td></tr>
<tr><td> Implementation </td><td> link to the PR for the implementation, NA if not availble </td></tr>
</table>

## Abstract

Bioconda and Conda Forge have occasionally transferred package recipes between them. The `strict` channel
priority recommended to Bioconda users and used when building packages on the Bioconda channel poses an
issue when transfers occur, especially in the from Conda Forge to Bioconda direction. This proposal aims to 
define a policy for standard procedure when such transfers occur. Included in this is the introduction
of a new label to be applied to all package builds that transfer from Conda Forge to Bioconda.

## Specification

### Bioconda to Conda Forge Transfers

1. Packages on Bioconda considered for transfer should be marked with a ["Move to Conda Forge" label](https://github.com/bioconda/bioconda-recipes/labels/Move%20to%20Conda-Forge) in the `bioconda-recipes` repository. This label can be applied either in an Issue or a Pull Request.
2. A Pull Request should be create on Conda Forge's `staged-recipes` repository to add the
3. Linter behavior flags adding a recipe already hosted by Bioconda. This will trigger a message to the conda-forge/bioconda-recipes team.

## Other sections

Other relevant sections of the proposal.  Common sections include:

    * Specification -- The technical details of the proposed change.
    * Motivation -- Why the proposed change is needed.
    * Rationale -- Why particular decisions were made in the proposal.
    * Backwards Compatibility -- Will the proposed change break existing
      packages or workflows.
    * Alternatives -- Any alternatives considered during the design.
    * Sample Implementation -- Links to prototype or a sample implementation of
      the proposed change.
    * FAQ -- Frequently asked questions (and answers to them).
    * Resolution -- A short summary of the decision made by the community.
    * Reference -- Any references used in the design of the CFEP.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
