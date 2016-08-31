
<table>
<tr><td> Title </td><td> CFEP Purpose and Guidelines </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Jonathan J. Helmus &lt;jjhelmus@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Aug 30, 2016</td></tr>
<tr><td> Updated </td><td> Aug 30, 2016</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

# Abstract

CFEPs are the manner in which the conda-forge community suggests changes to the
project, and outlines how the suggested changes are reviewed.  This document provides details on what a CFEP is, how to author one, and the workflow used to review these proposals.

# What is a CFEP

CFEP stands for Conda-Forge Enhancement Proposal.  A CFEP is a document which
outlines a suggested change to how the project operates.  These changes may be
technical in manner or may deal with social aspects of the project including
governance and conduct.  The CFEP should provide a concise description of the
proposed change as well as the rationale for the change.

CFEPs are intended to be the primary mechanism for proposing major changes to
how conda-forge operates and for documenting both the changes themselves and the
decision making process.

CFEPs are maintained as text files using Markdown for formatting in the
[conda-forge/conda-forge-enhancement-proposals](https://github.com/conda-forge/conda-forge-enhancement-proposals)
repository. Their revision history is a historic record of the proposed change.

# Workflow

The CFEP process begins with an idea for a change in how the conda-forge
organization operates.  These change can be technical or address the
social aspects of the organization.  Small changes often do not need a CFEP and
can be discussed on the
[conda-forge gitter channel](https://gitter.im/conda-forge/conda-forge.github.io),
the [conda-forge mailing list](https://groups.google.com/forum/#!forum/conda-forge),
or as part of a conda-forge meeting.  More involved or controversial changes
should be submitted as CFEPs.  When it is unclear if a change requires a CFEP,
ask in one of the above forums and the conda-forge core team should be able to
provide guidance.  The conda-forge core team may solicit CFEPs on topics of
concern to the community.  CFEPs should focus on a single issue, broad changes
should be split into multiple well-focused CFEPs.

Each CFEP must have a champion or champions -- someone who will write the
CFEP in the format described below, submit the CFEP as a pull request,
follow the discussion on the pull request, and answer questions.  Before
beginning to write a proposal, it is best to ascertain if the idea is CFEP-able.
A discussion on the
[conda-forge gitter channel](https://gitter.im/conda-forge/conda-forge.github.io),
the [conda-forge mailing list](https://groups.google.com/forum/#!forum/conda-forge),
or as part of a conda-forge meeting is the best way to go about this.

Once the champion has asked the conda-forge community as to whether an idea
has any chance of acceptance, a draft CFEP should be written following the
template in [CFEP-00](cfep-00.md) and described below.

The CFEP draft may be circulated to interested parties for informal comment and
review prior to a formal submission. During this time, the Status of the CFEP
should be set to Draft.

## Submission

Once complete, this draft should be submitted as a pull request to the
[conda-forge/conda-forge-enhancement-proposals](https://github.com/conda-forge/conda-forge-enhancement-proposals)
repository with the Status changed to Proposed.  At this point, members of the
conda-forge community will review the submission.

## Review

CFEP review will begin once a pull request has been made.  All members of the
conda-forge community are welcome and encouraged to participate in the review
of CFEPs in a civil and respectful manner.  The CFEP champion(s) should be
prepared to answer questions on the proposal and make updates to the
proposal as recommended by the community.

## Resolution

All CFEPs will be resolved as either *Rejected*, *Accepted* or *Deferred*
depending on the consensus of the community. Once the community has reached a
consensus, the CFEP champion should update the Status of proposal and add a
link to the discussion to the table.  Regardless of the resolution, the pull
request should be merged once these tasks have been done.

## CFEP Maintenance

For *Rejected* CFEPs, no modifications are made after the pull request is merged.
*Accepted* CFEPs should be updated with a link to the pull request(s) of the
implementation and the status changed to *Implemented* when the proposed
changes have been implemented.  *Deferred* CFEPs can be re-submitted in a new
pull request after an appropriate amount of time.

# CFEP Content

All CFEPs should begin with a top level table with the following information:

<table>
<tr><td> Title </td><td> A short title of the proposal </td>
<tr><td> Status </td><td> Draft | Proposed | Accepted | Rejected | Deferred | Implemented </td></tr>
<tr><td> Author(s) </td><td> Full Name &lt;full.name@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Aug 30, 2016</td></tr>
<tr><td> Updated </td><td> Aug 30, 2016</td></tr>
<tr><td> Discussion </td><td> link to the issue where the CFEP is being discussed, NA before submission </td></tr>
<tr><td> Implementation </td><td> link to the PR for the implementation, NA if not available </td></tr>
</table>

This table should be followed by a **Abstract** section and then
additional sections as needed by the proposal.  Some section that may be
included if appropriate for the proposal include.

    * Specification -- The technical details of the proposed change.
    * Motivation -- Why the proposed change is needed.
    * Rationale -- Why particular decisions were made in the proposal.
    * Backwards Compatibility -- Will the proposed change break existing
      packages or workflows.
    * Alternatives -- Any alternatives considered during the design.
    * Sample Implementation -- Links to prototype or a sample implementation of
      the proposed change.
    * FAQ -- Frequently asked questions (and answers to them).
    * Reference -- Any references used in the design of the CFEP.

A final **Copyright** section is also required.

## References

Much of this document was adapted from
[PEP 1 -- PEP Purpose and Guidelines](https://www.python.org/dev/peps/pep-0001/)which has been placed in the public domain.
The [IPEPs: IPython Enhancement Proposals](https://github.com/ipython/ipython/wiki/IPEPs:-IPython-Enhancement-Proposals) was also referenced for inspiration.

## Copyright

This document has been placed in the public domain.
