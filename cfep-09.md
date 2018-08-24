
<table>
<tr><td> Title </td><td> Optional modules recipe template </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Mark Harfouche &lt;mark.harfouche@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Aug 20, 2018</td></tr>
<tr><td> Updated </td><td> Aug 20, 2018</td></tr>
<tr><td> Discussion </td><td> link to the PR where the CFEP is being discussed, NA is circulated initially </td></tr>
<tr><td> Implementation </td><td> link to the PR for the implementation, NA if not availble </td></tr>
</table>

## Abstract

Many python packages come with the ability to have different sets of features
depending on what other libraries are available at run time. Unfortunately,
the standard recipe avaible in the `staged-recipes` repo doesn't guide new
packagers in an obvious way that would help them specify these
optional dependencies. Furthermore, the PyPI
braket syntax for subpackages is incompatible with conda syntax. This leads
to much confusion for those moving from PyPI to conda and conda-forge when
their familiar syntax is lost. This CFEP aims at resolving those two issues
by providing a usable template for PyPI packages looking to create a recipe
template with optional modules.

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
dependencies than their PyPI counterparts, Dask on conda-forge installs
numpy, pandas, and the distributed packages, Matplotlib installs PyQt5 increasing
the size of many installations. On PyPi matplotlib and dask give full control
to the users of the packages which options they desire to install.

New developers may see this as an advantage of conda (and conda-forge) since
`conda` packages seem like *batteries-included* versions of PyPI counterparts.
Unfortunately, this strategy hinders a maintainers ability to be expressive
about their dependencies.  Furthermore, it makes it almost impossibile to
create small environments that only include the subset of the functionality one
needs.

# Timeliness of CFEP

There has been increased interest in having many options for the matplotlib
package. The ubiquity of matplotlib accross the python ecosystem might mean
that the chosen naming convention, and decisions made for that subpackage would
be followed by other packages. Therefore, it is important that the matplotlib
split be handled in a way that will not create increased confusion on
conda-forge when more packages decides to have optional dependencies.

# Rationale

While this may have only been possible with metapackages in previous versions
of conda, conda3 seems to allow for multiple outputs to be specified in a
single package.

For python packages that are typcally compiled with all of their dependencies,
this is an easy way to implement optional dependencies. Unfortunately, little
documentation exists on how to write a package that would meet the conda-forge
standards and offer users this feature.

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

1. How the `core` subpackage might be defined.
1. How one optional subpackage can be included as part of the package.
1. How the `all` variant can be defined as part of the package.
1. The naked package specification can be defined by pointing to `core` or `all`

# Naming convention

The author of this CFEP recommends using the `.` (dot) delimeter to specify
subpackages because

1. This syntax is familiar for python users. Importing a `dot` module typically
   refers to a subpackage or an additional feature a user might want.  For
   certain python packages, this naming convention could follow the name of the
   interested submodules that users might be interested in. For example
   `dask.array` and `dask.bag` would what appear on a user's importing
   statements.
2. The suffixed `dashes` have started to appear as mutually exclusive variants.
   For example `tensorflow-gpu` and `tensorflow-cpu`.  Only one of these two
   packages can be installed at once. This would create confusion when trying
   to install `matplotlib-pyqt5` and `matplotlib-pygtk` which can both be
   installed at the same time.

# Sample implementation
TODO


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
