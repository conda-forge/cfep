
<table>
<tr><td> Title </td><td> conda-forge / Bioconda Transfer Policy </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Mervin Fansler &lt;mervin.fansler@bric.ku.dk&gt;</td></tr>
<tr><td> Created </td><td> 10 July 2024</td></tr>
<tr><td> Updated </td><td> 19 September 2024</td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/cfep/pull/52 </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Bioconda (BC) and conda-forge (CF) have occasionally transferred package recipes between them. The `strict` channel
priority recommended to BC users and used when building packages on the BC channel poses an
issue when transfers occur, especially in the CF-to-BC direction. This proposal aims to 
define a policy for standard procedures when such transfers occur. Included in this is the introduction
of a new label to be applied to all package builds that transfer CF-to-BC.

## Specification

### Bioconda to conda-forge (BC-to-CF) Transfers

1. Packages on BC considered for transfer should be marked with a ["Move to conda-forge" label](https://github.com/bioconda/bioconda-recipes/labels/Move%20to%20Conda-Forge) in the `bioconda-recipes` repository. This label can be applied either in an Issue or a Pull Request.
2. A Pull Request should be create on CF's `staged-recipes` repository to add the recipe. A link to the labeled BC Issue/Pull Request should be included in the body of the CF Pull Request.
3. The linter will flag when adding a recipe already hosted by BC. This will trigger a message to [the **conda-forge/bioconda-recipes** team](https://github.com/orgs/conda-forge/teams/bioconda-recipes).
4. Upon approval by [the **conda-forge/bioconda-recipes** team](https://github.com/orgs/conda-forge/teams/bioconda-recipes), the Pull Request can then be reviewed and merged.
5. A Pull Request should be created (or edited) on `bioconda-recipes` that removes the recipe. A link to the CF `staged-recipes` Pull Request should be documented in the Pull Request text.

### conda-forge to Bioconda (CF-to-BC) Transfers

1. An Issue should be created on the CF feedstock with the title "Move to Bioconda". [The **conda-forge/bioconda-recipes** team](https://github.com/orgs/conda-forge/teams/bioconda-recipes) can also be pinged for their input.
2. A Pull Request should be created on the `conda-forge/admin-requests` feedstock to label all previous builds of this feedstock with a `transferred-to-bioconda` label. It should also initiate a request from the Core team to archive the feedstock. [The **conda-forge/bioconda-recipes** team](https://github.com/orgs/conda-forge/teams/bioconda-recipes) should be pinged to review.
3. A Pull Request on BC's `bioconda-recipes` should be created to add the recipe there. A link to this Pull Request should be added to the above Issue and Pull Request.
4. Once a link is provided and a [**conda-forge/bioconda-recipes**](https://github.com/bioconda/bioconda-recipes) member has reviewed the Pull Request on [`conda-forge/admin-requests`](https://github.com/conda-forge/admin-requests), **conda-forge/core** should proceed to review and merge.
5. The BC `bioconda-recipes` Pull Request can be merged after [the **conda-forge/bioconda-recipes** team](https://github.com/orgs/conda-forge/teams/bioconda-recipes) has approved the `conda-forge/admin-requests` Pull Request.

### Expected Implementation

1. An new option `cfep21_label` will be added to [`conda-forge/admin-requests`](https://github.com/conda-forge/admin-requests).
2. Once implemented, the following information will be added to the conda-forge Documentation:
   - A section on BC-to-CF transfer will be added under "Maintainer Documentation > Contributing packages".
   - A section on CF-to-BC transfer will be added under "Maintainer Documentation > Maintaining packages".
   - A section on using the `label/transferred-to-bioconda` label to solve environments requiring the labelled artifacts will be added under "User Documentation > FAQ".

## Motivation

BC package builds use a `strict` channel priority with the channels `conda-forge > bioconda > defaults`. This creates a situation that when a package transfers from CF to BC the presence of builds with `main` label in `conda-forge` masks the solver from considering builds in a lower priority channel.

## Rationale

This CFEP proposes to resolve CF-to-BC transfer issues by moving `conda-forge` builds off the `main` label and onto a dedicated label. This approach relies on a metadata update to hide the artifacts in `conda-forge` channel so that artifacts in `bioconda` channel become visible in `strict` channel priority. By using a dedicated label, end-users will also have a means of re-enabling search of these older packages by specifying the label when solving.

## Alternatives

Transferring artifacts (e.g., as in [CFEP-3](https://github.com/conda-forge/cfep/blob/main/cfep-03.md), followed by removal) would also solve the end-user issues. However, this would can be regarded as a more drastic change compared to a labeling approach.

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