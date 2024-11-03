
<table>
<tr><td> Title </td><td> More Secure Package Name Approval Process </td>
<tr><td> Status </td><td> Accepted </td></tr>
<tr><td> Author(s) </td><td> Matthew R. Becker &lt;becker.mr@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Oct 12, 2024</td></tr>
<tr><td> Updated </td><td> Nov 3, 2024</td></tr>
<tr><td> Discussion </td><td> #55 </td></tr>
<tr><td> Implementation </td><td> <a href="https://github.com/conda-forge/core-notes/issues/30">conda-forge/core-notes/issues/30</a> </td></tr>
</table>

## Abstract

Our current package upload process allows current members of `conda-forge` to upload a package with any name not currently used by another feedstock. This policy is insecure since it allows for malicious activity like typo squatting. This CFEP proposes a more secure package name approval process.

## Specification

The new package name approval process will be as follows:

- New packages submitted to `staged-recipes` are reviewed by a human and so have their names automatically approved when the recipe is merged. Note that `staged-recipes` already lints for name conflicts, but we rely on our `staged-recipes` reviewers to catch any issues.
- New packages created by adding outputs to an existing feedstock will go through an approval process whereby a member of `conda-forge/core` approves the package name.
- For feedstocks that generate new names in a programmatic way (e.g., a compiler), an allow-list of acceptable name patterns will be maintained. A new package name from a feedstock that in the allow-list that matches one of its name patterns will be automatically approved.

## Rationale

The process above always has a human approve new package names or patterns of names. The allow-list of acceptable name patterns will reduce the burden on feedstock maintainers and reviewers for feedstocks that generate new names in a programmatic way.

## Implementation

- The rules above are fully implemented in `conda-forge/admin-requests` via its [`add a package output`](https://github.com/conda-forge/admin-requests/tree/main?tab=readme-ov-file#add-a-package-output-to-a-feedstock) request-type.
- The [allow-list of acceptable name patterns](https://github.com/conda-forge/feedstock-outputs/blob/main/feedstock_outputs_autoreg_allowlist.yml) is located in the `conda-forge/feedstock-outputs` repository.
- Our package upload infrastructure has been updated to enforce the rules above if the `auto_register_all` setting in the [`config.json`](https://github.com/conda-forge/feedstock-outputs/blob/main/config.json) file in `conda-forge/feedstock-outputs` is set to `false`.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
