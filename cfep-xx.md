<table>
<tr><td> Title </td><td> A more unixy layout for cpython on windows </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Isuru Fernando &lt;ifernando@openteams.com&gt;</td></tr>
<tr><td> Created </td><td> May 14, 2026</td></tr>
<tr><td> Updated </td><td> May 14, 2026</td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/python-feedstock/issues/860 </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

A more unixy layout for cpython package and downstreams on windows
is proposed changing the places where python headers, libraries,
site-packages and stdlib are placed. This proposal is for upcoming
python 3.15 release and is not intended to be backported to older
python releases.

## Motivation

Currently, the package layout for all conda packages
are dictated by the python package. All C/C++ libraries, header
files end up in <CONDA_PREFIX>/Library because <CONDA_PREFIX> is
reserved for python due to its peculiarities on windows.
This CFEP proposes changing this so that other libraries can use
<CONDA_PREFIX>/lib, <CONDA_PREFIX>/include if they choose to.

## Specification

| Component            | Current           | Proposed                 | Unix reference                 | 
| -------------------- | ----------------- | ------------------------ | ------------------------------ |
| purelib/platlib      | Lib/site-packages | lib/python/site-packages | lib/python3.14t/site-packages  |
| scripts              | Scripts           | Scripts                  | bin                            |
| stdlib/platstdlib    | Lib               | lib/python               | lib/python3.14t                |
| include/platinclude  | include           | include/python           | include/python3.14t            |
| platlibdir           | DLLs              | DLLs                     | lib (not used)                 |

## Implementation

### python package support

We propose implementing this CFEP for CPython 3.15 and up.
This can be implemented by adding a `nt_conda` install scheme to [sysconfig](
https://github.com/python/cpython/blob/v3.15.0b1/Lib/sysconfig/__init__.py#L28-L60)
and then making it the default.

See [debian patch](https://salsa.debian.org/cpython-team/python3/-/blob/python3.14/debian/patches/sysconfig-debian-schemes.diff?ref_type=heads) to see how they override the scheme to
make the distinction between `dist-packages` (apt installed python packages)
and `site-packages` (pip installed python packages).

### conda support

conda clients are only concerned with the location of the `scripts` and
`purelib`/`platlib` components. The former needs to be added to `PATH`
and both are needed for placing files in the correct layout for
`noarch: python` packages. The others (`stdlib/platstdlib`, `platlibdir`
and `include/platinclude`) can be changed without breaking anything.

1. purelib/platlib

   For this case we have `python_site_packages_path` and therefore all conda
   clients support this.

2. scripts

   We are intentionally not changing the layout here because there's no way to
   tell conda to install things here for `noarch: python` packages.
   I propose adding a `python_scripts_path` as a CEP and use that in a future
   release, but is out of scope for this CFEP.

## Rationale for locations

1. purelib/platlib

   This matches unix, but removes the version and ABI flags. This is done to
   have a version neutral location like it is in current windows layout.
   Note: the author would also like to change the unix reference to match
   the proposed windows layout, but is out-of-scope for this CFEP.

2. scripts

   No change because of conda limitation. See the previous section.

3. stdlib/platstdlib

   Same rationale as purelib/platlib

4. include/platinclude

   Same rationale as purelib/platlib

5. platlibdir

   These are internal places where `.pyd` and `py.ico` are installed and is
   internal to cpython package. The directory `DLLs` does not interfere with any
   other package, so we propose to defer the decision to conda-forge/python
   maintainers.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
