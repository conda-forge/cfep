
<table>
<tr><td> Title </td><td> Branches for Globally Pinned Packages </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Christopher J. "CJ" Wright &lt;cjwright4242@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> May 30, 2020</td></tr>
<tr><td> Updated </td><td> May 30, 2020</td></tr>
</table>

## Abstract

The CF global pinnings and the migrations that update the pinnings are important to maintaining consistancy across the CF ecosystem.
However, the discrepency between the current pin and the version on the master (usually only) branch on the feedstock creates issues.

For instance, if an update needs to be applied to a pinned package (potentially due to a depdendency migration) and the master branch has moved past the pin then a branch needs to be made for the pin.
This CFEP proposes to automatically create branches for the pinned versions of pinned packages.

This approach would have signifigant benefits:
1. Make migrations easier for the bot, since the bot can issue PRs into the master and pinned branches
2. Enable backporting of fixes for pins
3. Provide the groundwork for backporting of updates to packages

## Specification
On merge of a PR into the pinned feedstock detect if the new version is beyond its stated compatibility, if so create a branch.
The bot will only issue migration PRs into master and branch versions which are listed in the pinnings or a migration.

The branches will need a consistent naming, for instance for boost `1.70.x`.
The `x` acts as a filler for parts of the version number not provided by the `max_pin`.

The bot will open version bump PRs matchign that version spec (for instnace, `1.70.1`) into that branch.

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
