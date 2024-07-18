
<table>
<tr><td> Title </td><td> Conda Forge-Bioconda Transfer Policy </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Mervin Fansler &lt;mervin.fansler@bric.ku.dk&gt;</td></tr>
<tr><td> Created </td><td> 10 July 2024</td></tr>
<tr><td> Updated </td><td> 18 July 2024</td></tr>
<tr><td> Discussion </td><td> link to the PR where the CFEP is being discussed, NA is circulated initially </td></tr>
<tr><td> Implementation </td><td> link to the PR for the implementation, NA if not availble </td></tr>
</table>

## Abstract

Bioconda (BC) and Conda Forge (CF) have occasionally transferred package recipes between them. The `strict` channel
priority recommended to BC users and used when building packages on the BC channel poses an
issue when transfers occur, especially in the CF-to-BC direction. This proposal aims to 
define a policy for standard procedure when such transfers occur. Included in this is the introduction
of a new label to be applied to all package builds that transfer CF-to-BC.

## Specification

### Bioconda to Conda Forge (BC-to-CF) Transfers

1. Packages on BC considered for transfer should be marked with a ["Move to Conda Forge" label](https://github.com/bioconda/bioconda-recipes/labels/Move%20to%20Conda-Forge) in the `bioconda-recipes` repository. This label can be applied either in an Issue or a Pull Request.
2. A Pull Request should be create on CF's `staged-recipes` repository to add the
3. The linter will flag when adding a recipe already hosted by BC. This will trigger a message to [the **conda-forge/bioconda-recipes** team](https://github.com/orgs/conda-forge/teams/bioconda-recipes).
4. Upon approval by a **conda-forge/bioconda-recipes** team member, the Pull Request can then be reviewed and merged.
5. A Pull Request should be created (or edited) on `bioconda-recipes` that removes the recipe. A link to the CF `staged-recipes` Pull Request should be documented in the Pull Request text.

### Conda Forge to Bioconda (CF-to-BC) Transfers

1. An Issue should be created on the CF feedstock with the title "Move to Bioconda". The **conda-forge/bioconda-recipes** team can also be pinged for their input.
2. A Pull Request should be created on the `conda-forge/admin-requests` feedstock to label all previous builds of this feedstock with a `transferred-to-bioconda` label. It should also initiate a request from the Core team to archive the feedstock. The **conda-forge/bioconda-recipes** should be pinged to review.
3. A Pull Request on BC's `bioconda-recipes` should be created to add the recipe there. A link to this Pull Request should be added to the above Issue and Pull Request.
4. Once a link is provided and a **conda-forge/bioconda-recipes** member has reviewed the Pull Request on `conda-forge/admin-requests`, CF Core should proceed to review and merge.
5. The BC `bioconda-recipes` Pull Request can be merged after **conda-forge/bioconda-recipes** has approved the `conda-forge/admin-requests` Pull Request.

## Motivation

BC package builds use a `strict` channel priority with the channels `conda-forge > bioconda > defaults`. This creates a situation that when a package transfers from CF to BC that the presence of builds with `main` label in `conda-forge` masks the solver from considering builds in lower priority channel. This CFEP proposes to resolve this by moving `conda-forge` builds off the `main` label and onto a dedicated labe. By using a dedicated label for this, we provide end-users with a means of re-enabling search of these older packages by explicitly specifying the label when solving.

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
