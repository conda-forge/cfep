
<table>
<tr><td> Pinning Proposal System for Automatic Rebuilds</td><td> Pinning Proposal </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Christopher J. "CJ" Wright &lt;cjwright4242@gmail.com&gt; Matthew R. Becker &lt;cbecker.mr@gmail.com&gt;</td></tr>
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

## Motivation
Currently when we move pinnings we move the pin in the ``conda-forge-pinning-feedstock``.
Then we start a migration using the ``regro-cf-autotick-bot``.
This order of operations causes some issues, when new feedstocks
are created they recieve the new pinnings even if their dependencies
are not rebuilt with the new pinnings themselves.

## Specification

### The migrations proceed as follows:
1. We make a PR on the `migrations` repo with a change to the global pinnings file, generating a new version. 
2. Once the change is merged, the bot picks it up and starts going through the graph in topo order. 
3. A migration PR is issued for a node only if 
    1. The node depends on the changed pinnings.
    2. It has no dependencies that depend on the new pinnings and have not been migrated.
    3. It is not currently being migrated by an earlier migration. Per @scopatz, this state is stored in `libcflib` with a REST API.
4. Process 3 continues until we determine that the migration is sufficiently complete and the change is merged into the global pinnings file. 

### Implementation Details:
- We would need to associate a version independent of git hash with each migration that orders them in time and thus orders the pinning files in time. A pinnings file could be versioned simply with a comment inserted at the top of the YAML containing the timestamp that the change was merged or something. We could use seconds from the epoch!
- For a given migration, we only use the changes up to and including the commit that has the relevant pinning updates, even if further updates are merged for future migrations. This forces a single global pinning file to be used for a given subgraph that is being migrated. This seems required for consistency.
- We allow for a local pinnings file to be present in each feedstock that would override the global pinnings if the version is later. In other words, the more recent pinnings file is always preferred by smithy. 
- Smithy should use the most recent pinnings file when building the `.ci_support` files. This makes sure that rerendering the feedstock always produces the correct pinnings.
- The migration bot would write the new pinnings file as the local one, rerender the repo and then issue the PR.
- Migration changes have to be merged into the global pinnings file in the order they were made.

## Rationale

### Multiple migrations of the same package:
We don't want to allow more than one change to the pinnings of a given node at a time, mostly because we cannot guarantee that later pinning migrations can be mixed with earlier ones (e.g., both could touch the same pins for some weird reason). We need to ensure that all nodes in the subgraph that share the same pinned dependence are uniformly migrated from one version to the next. However, we can have more than one migration in flight at a time simply because they could touch different parts of the graph. 

### The rule of preferring the most recent pinnings file covers all of the relevant cases
 - If a feedstock has no local pinnings, then that is fine. 
 - If the file is present, but matches the global pinnings, this is also fine.
 - In the middle of a migration, a feedstock that is being migrated would have a local pinnings file that should be used ahead of the global one. The local version would be ahead of global in this case since the migration bot would add the new pinnings file as the local one.
 - After a migration has ended and the changes from the migration are merged to the global pinnings file, then the global file will always be at the same version as the local one, or at a later version.
- If some other migration comes through later, it would then overwrite the same local pinnings file with a version that is by definition later than the current global one. 
- If a feedstock adds a dependency that has a pinned version, then the version that gets used is from the most recent pinnings file between the global one and the local one. 
- Adding a dependence in the middle of a migration would cause the repo to be detected by the bot and a migration PR would be issued.

Thus we don't need to have conda-smithy cleanup any old local pinnings files, though it could if we want.


## Backwards Compatibility

This proposal would require that all changes to the global pinnings file have to be routed through the new `migrations` repo. No human would make a PR on the global pinnings repo and instead that repo would be managed entirely by the bot. 

## Alternatives

1. Earlier versions of this proposal did not direct version the global pinnings files in a time ordered fashion. Instead they simply always preferred the local pinnings over the global ones. However, this procedure fails in the case of a packahe adding a new dependence with a pin and it having an outdated local pinnings file. By having a version stored in the file, `conda-smithy` can tell which version is more recent.

2. An earlier version of this propsal was desgined around a migration being a YAML snippet. However, `conda_build_config.yaml` files can have conda-build selectors in them (e.g., `# [osx]`). This conda-build feature makes it pretty hard to use a YAML snippet as a patch on th global pinnings file. Using a direct copy of the proposed new global pinnings file is simpler. This procedure also allows us to migrate almost anything in the file. 

3. Some suggestions have been centered around directly looking at the package versions in the pinnings file to determine which one is newer. This procedure has some flaws. First, it does not allow us to downgrade pinnings if needed. It is ambiguous in the case that we migrate more than one package where one version goes up and the other goes down. Finally, it does not cover changes to things like selectors or zip keys which don't have version associated with them. 

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
