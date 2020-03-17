<table>
<tr><td> Title </td><td> Deprecate Python 2.7 and vs2008 </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Matthew R. Becker &lt;becker.mr@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Mar 9, 2020</td></tr>
<tr><td> Updated </td><td> Mar 16, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> https://github.com/conda-forge/conda-forge-pinning-feedstock/pull/491 </br> https://github.com/conda-forge/conda-forge.github.io/pull/999 </br> https://github.com/conda-forge/conda-forge-webservices/pull/345 </td></tr>
</table>

## Abstract

Python 2.7 is officially deprecated. `conda-forge` will follow suit.

## Motivation

Python 2.7 has been officially deprecated by the upstream developers and Anaconda Inc.
Further, vs2008, used to build Python 2.7, is not supported on Azure. Thus we will
remove vs2008 in this CFEP as well.

## Specification

- We will remove Python 2.7 from the global pinnings.
- We will post a notice in the documentation that Python 2.7 is deprecated.
- We will provide an admin command that will add back Python 2.7 to any repo
  on an opt-in basis.
- We will provide no ongoing support for Python 2.7 builds and any builds are
  provided on an "as-is" basis.
- We will remove vs2008 as a compiler in conjunction 
  with the deprecation of Python 2.7.
- We will add a `cf202003` label to all packages in main.

## Reference

See the discussion on issue [#20](https://github.com/conda-forge/cfep/issues/20).
A migration to drop Python 2.7 would be time-consuming and is not formally needed.
Thus I left it out of this CFEP.

## Copyright

This document is [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
