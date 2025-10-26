
<table>
<tr><td> Title </td><td> Guidelines for renaming packages </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td>
    Jaime Rodríguez-Guerra &lt;jaime.rogue@gmail.com&gt;
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

### Unclaimed names

When an existing package wants to be renamed to a currently unclaimed name, the process is uncontroversial and straight-forward. Two approaches are possible:

- The new name needs to be registered to the originating feedstock in `feedstock-outputs` (or equivalent). Once approved, a PR in the feedstock adding the new output may proceed normally. The old name is usually kept as an alias output for backwards compatibility. In this case, the feedstock retains the name of the original name.
- The original feedstock is archived and the package is re-submitted to `staged-recipes` (or equivalent) under the new name. The old package name should be added as an alias output for backwards compatibility, and registered as a valid output of the new feedstock in `feedstock-outputs`.

### Names in use

The blanket policy in this case is that an existing package name may not be used by a different project. In exceptional cases, the old name may be reallocated provided that:

- The old project is either inactive or abandoned.
- There are ways to install both projects without conflicts or mixups. This includes ensuring that version numbers do not overlap and that repodata patches are applied unambiguously. When necessary, measures like marking old packages as broken or moving them to a new label may be applied.

The original feedstock may not be reused, and must be archived.

In case of disagreement, the core team may resolve the issue at their discretion.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
