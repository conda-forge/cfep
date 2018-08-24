<table>
<tr><td> Title </td><td> Optional modules recipe template </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Mark Harfouche &lt;mark.harfouche@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Aug 20, 2018</td></tr>
<tr><td> Updated </td><td> Aug 20, 2018</td></tr>
<tr><td> Discussion </td><td> [PR on CFEP](https://github.com/conda-forge/conda-forge-enhancement-proposals/pull/12) </td></tr>
<tr><td> Implementation </td><td> Example for the [dask-feedstock](https://github.com/hmaarrfk/dask-feedstock/pull/1) </td></tr>
</table>

## Abstract

Many python packages come with the ability to have different sets of features
depending on what other libraries are available at run time. Unfortunately,
the standard recipe avaible in the `staged-recipes` repo doesn't guide new
packagers to keep these dependencies optional.
This CFEP aims to help new packagers by providing them a robust example
that they can adapt to add constrained optional dependencies and subpackages.

# Motivation

Many large Python modules can be installed with *compatible* options. For
example, matplotlib comes with the ability to select between many
backends at runtime, allowing for one script in the same environment to use the
Tk backend, while an other uses a Qt backend. Dask provides an API for for lazy
computation in python but often people find themselves not using many of the
features that it provides. Using pip and PyPI, users can specify which options
they have a need for using a square braket notation to only pull in what they
need. This notation is incompatible with conda as square brakets are used for
extended match specification à la


```
conda-forge::python[version=3.6.5, build_number=1]
```
[@mbargull].

To circumvent this problem, conda-forge packages tend to pull in many more
dependencies than their PyPI counterparts. The `dask` package on conda-forge is
a metapackage that depends on `dask-core` defined in a different feedstock and
installs numpy, pandas, and the distributed packages by default. It does not
provide an easy way for users to install the `delayed` or `distributed`
features without pulling in numerical packages. Matplotlib installs PyQt5
increasing the size of many installations.  On PyPI, dask gives
full control to the users of the packages which options they desire to install by
adding suffixes to the package names.

New developers sometimes see the large number of default packages as an advantage
of conda over other packaging solutions. The packages on conda (and conda-forge)
often seem to "just work" as all they are typically *batteries-included* compared
to the naked packages on PyPI.
Unfortunately, the current strategy hinders a maintainers ability to be expressive
about their dependencies.  Furthermore, it makes it almost impossibile to
create small environments that only include the subset of the functionality one
needs.

# Rationale

While this may have only been possible with metapackages in previous versions
of conda, conda3 allows for multiple outputs to be specified in a
single package.

For python packages that are typcally compiled with all of their dependencies,
this is an easy way to implement optional dependencies. Unfortunately, little
documentation exists on how to write a package that would meet the conda-forge
standards and offer users this ability to create small environments.

# Backwards compatibility

We recommend that any existing packages retain their `batteries-included`
variant as the default installation as not to break downstream packages. New
packages should have a choice to have the default package point to either the
`core` subpackage with minimal dependencies, or the `all` subpackage, with most
dependencies include (except those used excelusively for testing and
development).

The sample recipe should point to the `all` variant to ensure that
`conda-forge` remains a battery-included distribution for new users.

# Specification

This CFEP proposes that we provide within the `staged-recipes` repository outlining
the best practices involved in creating a single `meta.yaml` as an example outlining

1. How the `core` subpackage might be defined, compiled, and installed.
1. How one optional subpackage can be included as part of the package.
1. How the `all` variant can be defined as part of the package.
1. How the naked package specification can be defined by pointing to `core` or `all`

# Naming convention

The author suggests using the dash `-` to delimit options in a subpackage. The
dash has had a few packages already use it to implement `core` and `naked`
variants such as
[dask-core-feedstock](https://github.com/conda-forge/dask-core-feedstock)
[dask-feedstock](https://github.com/conda-forge/dask-feedstock). Natural
extentions to these two subpackages would include packages such as `dask-array`
and `dask-distributed` though these would create undue burden on package
maintainers who would be required to maintain multiple recipe files.

It is unfortunate the the dash has also been used to identify mutually
exclusive packages, such as `tensorflow-gpu` and `tensorflow-cpu`.

## Alternatives
The dot might seem like a good alternative to the `-` as it may appear to
be more familar to python users who would see these options as other
modules they can import from a package.

Unfortunately, the author of this CFEP already found that if dask (for example)
moved to using a `.` would lead to both the new `dask.core` (from local) and
the old `dask-core` (from conda-forge) to be pulled in during testing. Should
conda-forge decide to make this move, it may become much more involved to
untangle the dependencies.

# Unfortunate side-effects

The `script` section inside an `outputs` block does not behave like the
`script` section in the `build` section. It must contain a filename.

This means that the beloved line (for unix):
```bash
$PYTHON -m pip install . --no-deps --ignore-installed --no-cache-dir -vvv
```
and
```bat
%PYTHON% -m pip install . --no-deps --ignore-installed --no-cache-dir -vvv
```

now needs to be encapsulated in a `build_package.sh` and `build_package.bat` file.

# Sample implementation

Please see the example for dask in [this
fork](https://github.com/hmaarrfk/dask-feedstock/pull/1)

```yaml
```

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
