
<table>
<tr><td> Pinning Proposal System for Automatic Rebuilds</td><td> Pinning Proposal </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Christopher J. "CJ" Wright &lt;cjwright4242@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Feb 20, 2019</td></tr>
<tr><td> Updated </td><td> Aug 30, 2016</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This CFEP proposes a change to how our internal pinning system works.
The new system will comprise of a new repo for proposed migrations.
To propose a migration a PR will be issued with a new version of
the global pinning file and some metadata about the migration needed 
(if any).
Once the PR is squashed and merged the bot will take the metadata
to calculate which packages need to be rebuilt and start issuing
PRs with the new pinning as a local pinning file to the feedstocks
in topo order.

This CFEP is taken from @beckermr's work on [conda-forge.github.io](https://github.com/conda-forge/conda-forge.github.io/issues/712).

## Introduction
Currently when we move pinnings we move the pin in the ``conda-forge-pinning-feedstock``.
Then we start a migration using the ``regro-cf-autotick-bot``.
This order of operations causes some issues, when new feedstocks
are created they recieve the new pinnings even if their dependencies
are not rebuilt with the new pinnings themselves.

## Specification
To change the pinnings a PR will be opened into the ``migration`` directory in a proposed pinning repo.
The PR will contain a complete copy of the global pinning file with any changes
needed for the migration and a metadata file which includes the name of the migration,
the source nodes which will be changed, and the finishing criteria (eg. 80% finished and 
``numpy`` must be migrated).
Once the commits are squashed and merged the bot will read the new file and start
issuing PRs in topo order which bump the build number, put a copy of the modified
pinning file into the repo for conda-smithy/build to find and rerendering the feedstock.
When the finishing criteria is met then the bot will issue a PR updating the global
pinning file.
Then the bot can either continue issuing PRs, this requires taking a snapshot
of the graph at that point in time, or stop issuing PRs for that migration.

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
