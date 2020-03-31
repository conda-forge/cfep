
<table>
<tr><td> Title </td><td> Setting license and license_family field </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Isuru Fernando &lt;isuruf@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> March 28, 2020</td></tr>
<tr><td> Updated </td><td> March 28, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> https://github.com/conda-forge/conda-smithy/pull/1268 and https://github.com/conda-forge/conda-forge-repodata-patches-feedstock/pull/36 </td></tr>
</table>

## Abstract

This document proposes that we enforce the use of SPDX license expressions
in license field in package metadata and to hotfix the package metadata
to add the license_family field when missing by automatically
parsing the license field.


## Motivation

license field in package metadata is in different formats and it'll be
useful for automated tools to have the license file in one format.
We are proposing that the format be SPDX license expression format.

license_family field in package metadata gives a family of metadata,
but has only a few specific allowed values and until now has been the
field that automated tools have used. When license field is standardized,
this field becomes redundant and can be automatically inferred.


## Rationale - Why SPDX

SPDX.org has all OSI approved licenses, FSF approved licenses and a few
other commonly used licenses.

SPDX also has a mechanism to use custom licenses using the
`LicenseRef-[0-9a-zA-Z\-.]*` format.

For packages with multiple licenses, SPDX license expressions provide
the conjunctions `AND` and `OR` to combine them.

SPDX format also has parsers to parse the format which makes it easy
to validate and also to infer details like the license_family


## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
