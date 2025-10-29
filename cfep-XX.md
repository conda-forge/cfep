
<table>
<tr><td> Title </td><td> Guidelines for renaming packages </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td>
    Jaime Rodríguez-Guerra &lt;jaime.rogue@gmail.com&gt; Matthew R. Becker &lt;becker.mr@gmail.com&gt
</td></tr>
<tr><td> Created </td><td> Oct 26, 2025 </td></tr>
<tr><td> Updated </td><td> Oct 26, 2025 </td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/admin-requests/pull/1711 </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This CFEP describes the principles informing how and when to rename a package in conda-forge.
Two cases are described depending on the status of the target name:

- Unclaimed names: A package already present in conda-forge wants to use a new name currently unclaimed.
- Names in use: A package, already present or about to be submitted, wishes to use a name already in use.

## Specification

### Feedstock Epochs

In order to track how feedstocks are reused, we define the concept of the `feedstock epoch` and a `feedstock epoch label`.

- When a feedstock is first created, its epoch is 0.
- If a feedstock is reused for a new project (see below), the `feedstock epoch` MUST be incremented by 1.
- A `feedstock epoch label` is a label of the form `{feedstock name without '-feedstock'}_feedstock_epoch{N}`. When the label is applied to an artifact, the label MUST reflect epoch of the feedstock at the time it produced the given artifact.
- All artifacts uploaded from a feedstock which do not otherwise have a `feedstock epoch label` are assumed to be from the current `feedstock epoch`.
- A `feedstock epoch label` MUST be applied to any artifact from the previous epoch of the feedstock.
- The current `feedstock epoch` for a given feedstock is determined as follows. The set of all labels for all artifacts produced by the feedstock is searched for any label matching the form of a `feedstock epoch label` for that feedstock. If no label matches, then the current `feedstock epoch` is 0. If labels do match, then the current `feedstock epoch` is one plus the maximum `feedstock epoch` over all matching labels.

### Unclaimed names

When an existing package wants to be renamed to a currently unclaimed name, the process is uncontroversial and straight-forward. Two approaches are possible:

- The new name needs to be registered to the originating feedstock in `feedstock-outputs` (or equivalent). Once approved, a PR in the feedstock adding the new output may proceed normally. The old name MUST be kept as an alias output for backwards compatibility. In this case, the feedstock retains the name of the original name.
- The original feedstock is archived and the package is re-submitted to `staged-recipes` (or equivalent) under the new name. The old package name MUST be added as an alias output for backwards compatibility, and MUST be registered as a valid output of the new feedstock in `feedstock-outputs`.

### Names in use

The blanket policy in this case is that an existing package name may not be used by a different project. In exceptional cases, the old name may be reallocated provided that:

- The old project is abandoned. Factors that can help to indicate if a project is abandoned include, but are not limited to, the project's git repo being archived, the project's distributions no longer being available (e.g., yanked artifacts on PyPI), etc.
- There are ways to install both projects without conflicts or unintentionally installing one project versus the other.
- The version number ranges of the projects do not intersect.
- The repodata patches for the old project (if any) do not change the repodata of the new project. Adjustments to the patches for the old project can be made, but they MUST produce the same modifications to the old artifacts.
- The old project's artifacts are all marked with the `broken` label. 
- The old project's artifacts are all marked with the correct `feedstock epoch label` (see above). 

The original feedstock MUST NOT be reused, and MUST be archived.

The core team may resolve any ambiguities or disagreements on feedstock reuse at their sole discretion. Further, the core team reserves the right to refuse to allow a feedstock to be reused at their sole discretion.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
