
<table>
<tr><td> Title </td><td> Export license packages for static and header-only libraries </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Daniel Ching &lt;carterbox@users.noreply.github.com&gt;</td></tr>
<tr><td> Created </td><td> Jul 9, 2022</td></tr>
<tr><td> Updated </td><td> Jul 9, 2022</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This CFEP proposes that header-only and static libraries and should be required
to run_export an empty package (containing their license) called "{{ name
}}-license" in order to ensure/automate attribution and to satisfy the license
requirements.

## Specification

Header-only or static library packages should include an output called
"{{ name }}-license" which is added as a run_export for the library.

## Motivation

Attribution for header-only and static libraries on conda-forge is not well
enforced because unlike shared libraries which are also required to be
installed again at runtime (with license included), these libraries need manual
intervention to ensure that the license of the header-only or static library is
concatenated to the license metadata of the downstream library which uses it.
This should obviously be done and is easy to notice for static libraries (whose
names end in "-static"), but for header-only libraries it is less obvious
because the name of the library does not obviously mark the library as
header-only.

Libraries like Eigen and Pybind11 are commonly used, but probably not
attributed beyond their mention in the host section of the recipe. Mention in
the requirements section of a recipe probably does not meet the
licensing/attribution requirements.

## Rationale

run_exports is the obvious way to deliver the license information into the
environment of downstream packages; this is already how the license information
is included for dynamically linked libraries.

"{{ name }}-license" matches the exisiting naming scheme for "{{ name }}-static"
"{{ name }}-includes".

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
