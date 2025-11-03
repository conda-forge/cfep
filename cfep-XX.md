
<table>
<tr><td> Title </td><td> Guidelines for (Re)Naming Packages </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td>
    Jaime Rodríguez-Guerra &lt;jaime.rogue@gmail.com&gt;, Matthew R. Becker &lt;becker.mr@gmail.com&gt
</td></tr>
<tr><td> Created </td><td> Oct 26, 2025 </td></tr>
<tr><td> Updated </td><td> Oct 26, 2025 </td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/admin-requests/pull/1711 </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This CFEP describes the principles informing how and when to (re)name a package in conda-forge.
Two cases are described depending on the status of the target name:

- Unclaimed names: A new package or a package already present in conda-forge wants to use a new name currently unclaimed.
- Names in use: A package, already present or about to be submitted, wishes to use a name already in use.

## Specification

This CFEP follows [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) in its use of certain keywords (e.g., MUST).

### Unclaimed Names

#### New Feedstocks & Name Squatting

- Unclaimed package names are typically available on a first-come-first-served basis and can be claimed by submitting a recipe for a package via `staged-recipes` (or equivalent).
- The core team reserves the right to prevent the use of certain names for any reason. Typical reasons the core team may prevent the use of a certain name include, but are not limited to, typo squating, ambiguity, violations of the code of conduct, conflicts with important infrastructure, etc.
- Pre-registering or squatting on a namespace in anticipation of future use is not allowed unless expressly approved by the core team via the consent of at least three core members. The core team MUST be `@`-mentioned on the relevant PR/issue and this PR/issue MUST stay open for at least one week.

#### Renaming an Existing Package

When an existing package wants to be renamed to a currently unclaimed name, the following steps apply:

- The new name MUST be registered to the originating feedstock in `feedstock-outputs` (or equivalent).
- Once the new name is approved, a PR in the feedstock adding the new output may proceed normally.
- One of the following two options to ease the transition to the new name from the old name MUST be used.
  1. The old name is kept as an alias output on the feedstock for backwards compatibility indefinitely.
  2. The old name is kept as an alias output on the feedstock for backwards compatibility for a limited period of time. In this case, the old name MUST be marked as deprecated through the addition of a linter rule and existing feedstocks using the old name MUST be actively migrated to the new name, either by hand or through an automated service (e.g., the autotick bot). After a suitable period of time, the old name may be dropped. Packages used by many other packages SHOULD be transitioned more slowly than packages which are not used by many other packages.

The core team reserves the right to prevent packages from being renamed for any reason at their sole discretion.

### Names in Use

The blanket policy in this case is that an existing package name may not be used by a different project. In exceptional cases, the old name may be reallocated provided that:

- The old project is abandoned. Factors that can help to indicate if a project is abandoned include, but are not limited to, the project's git repo being archived, the project's distributions no longer being available (e.g., yanked artifacts on PyPI), etc. For packages shipped on both conda-forge and PyPI, the package name on PyPI MUST have been inheritied by the new project from the old project.
- The two different projects can be unambiguously distinguished in the conda-forge repodata, in the sense of incompatible constraints for the package metadata (e.g., the old `foo` might only have builds that require `python <=2.7`, whereas the new `foo` would only have builds for `python >=3.10`, the version ranges do not intersect, etc.).
- The version ranges of the projects do not intersect.
- The repodata patches for the old project (if any) do not change the repodata of the new project. Adjustments to the patches for the old project can be made, but they MUST produce the same modifications to the old artifacts.
- The old project's artifacts are all marked with the `broken` label. 
- The original feedstock for the old project is not reused, and instead MUST be archived.

The core team may resolve any ambiguities or disagreements on feedstock reuse at their sole discretion. Further, the core team reserves the right to refuse to allow a feedstock to be reused at their sole discretion.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
