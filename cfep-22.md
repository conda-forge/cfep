
<table>
<tr><td> Title </td><td> ABI pinning via SONAME in build string </td>
<tr><td> Status </td><td> Draft | Proposed | Accepted | Rejected | Deferred | Implemented </td></tr>
<tr><td> Author(s) </td><td> Daniel Ching &lt;full.name@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Jul 22, 2022</td></tr>
<tr><td> Updated </td><td> Jul 22, 2022</td></tr>
<tr><td> Discussion </td><td>
  https://github.com/conda-forge/conda-forge.github.io/issues/610
  https://github.com/conda-forge/conda-forge.github.io/issues/157
  https://github.com/conda-forge/conda-forge.github.io/issues/150
</td></tr>
<tr><td> Implementation </td><td> link to the PR for the implementation, NA if not availble </td></tr>
</table>

## Abstract

Conda lacks a built-in method of tracking ABI separately from API. This is fine for python, whose ABI/API are effectively the same, but for compiled libraries it is not.
Trying to figure out what compatability guarantees that a project offers between API and ABI is annoying because you have to contact upstream maintainers and
manually monitor for changes. However, projects which care about ABI stability already offer this information in the form of the SONAME which is the version of the
ABI and is commonly added to the name of the shared library. This proposal is a standard procedure for adding the SONAME into the build string so that
the conda solver can prevent ABI breaks automatically.

## Motivation

You have to ask upstream about compatability guarantees. 
Many projects already track this information with the SONAME.

https://github.com/conda-forge/libavif-feedstock/pull/1#issuecomment-986310764
https://github.com/conda-forge/dav1d-feedstock/pull/1/files#r927851556
https://github.com/conda-forge/libwebp-feedstock/blob/615b5309a76ac96409394aa100ec11bb1c7ea150/recipe/meta.yaml#L14


## Backward Compatability

This proposal does not break exisiting packages. It is a feature that only helps future package builds.

## Alternatives

In theory breaking ABI changes can be introduced without changing the API. Reordering struct elements for example,
but project managers would in practice not also make a new API release for this change.

## Sample Implementation

Related: What do you think about putting the ABI major version in the build string? i.e.
david=1.0.0=b6hsdfbiwd where the SO major version is 6. Then we could pin to the API and ABI separately.

run_export:
  - {{ pin_subpackage( name|lower ) }} b{{ so_name_major }}h*

This would be templated out to

run_exports:
  - david >=1.0.0,<2 b6h*

or maybe

run_exports:
  - david >=1.0.0,<2
  - david * b6h*

So then the run export handles both API and ABI. The ABI version is between "b" and "h" because if it was just b6*, then we could run into issues when we reach b60.

The more C++ projects that I become the maintainer of, the more I run into the problem that conda doesn't have a built in way for tracking ABI separately from API (because conda's roots are in python where you don't worry about ABI).


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
