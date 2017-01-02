
<table>
<tr><td> Title </td><td> Providing {testing|dev|rc} builds on the conda-forge anaconda channel </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Eric Dill &lt;thedizzle@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sept 09, 2016</td></tr>
<tr><td> Updated </td><td> Sept 09, 2016</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

There are a number of recipe maintainers who have asked for guidance regarding
how to make testing builds available with the CI infrastructure on conda-forge.
This document outlines how those builds should be made available.

## Motivation

Pypi allows package maintainers to upload "prerelease" builds which users can
then install via

```
pip install PACKAGE_NAME --pre
```

The same semantics are slightly more cumbersome within the `conda` ecosystem but
are still manageable.  The analogous command with `conda` is

```
conda install -c conda-forge/label/rc -c conda-forge PACKAGE_NAME
```

We, as the conda-forge community need to arrive at a consensus for what labels
we will affix to packages so that there is a consistent user experience.

## Backwards Compatibility

There are only minimal concerns of backwards compatibility.  There are a few
relevant labels that package maintainers have affixed to their pre-release
packages. These are "pre" (1), "prerelease" (2), "rc" (2) and "test" (1).  The
numbers in parentheses represent the number of projects which have released
these pre-release packages.  With six total projects using non-main labels, I do
not think we have a serious concern with backwards compatibility due to changes
that come about via this proposal.

## Specification

Mark the pre-release package on anaconda.org one of the following labels
"testing", "dev" and "rc". The semantics of these labels should generally follow
the [guidelines](https://docs.python.org/devguide/devcycle.html#stages) that
Python itself follows.

- "rc": [Beta](https://docs.python.org/devguide/devcycle.html#beta) and [Release
  Candidate](https://docs.python.org/devguide/devcycle.html#release-candidate-rc)
  (RC). No new features. Bugfix only.

- "dev": [Pre-Alpha](https://docs.python.org/devguide/devcycle.html#pre-alpha)
  and [Alpha](https://docs.python.org/devguide/devcycle.html#alpha). More formal
  than "testing" but these are still packages that could see substantial changes
  between the dev version and the final release.

- "testing": Catch-all for things that are not in "dev" or "rc". This label
  should probably be reserved for testing the packaging process itself, not the
  code that the package contains.

If you wish to add numbers to your "dev" or "rc" you should follow the
[guidelines](http://conda.pydata.org/docs/spec.html#build-version-spec) put
forth by Continuum regarding version sorting in `conda`. Also see the [source
code for conda
4.2.13](https://github.com/conda/conda/blob/4.2.13/conda/version.py#L93-L119).
The tl;dr here is that conda sorts as follows:

```
< 1.0
< 1.1dev1    # special case 'dev'
< 1.1.0dev1  # special case 'dev'
== 1.1.dev1   # 0 is inserted before string
< 1.1.0rc1
< 1.1.0
```

So make sure that you ***tag*** your package in such a way that the package name
that conda-build spits out will sort the package uploaded with an "rc" label
higher than the package uploaded with the "dev" label.

### Auto-expiration of packages

Pre-release packages are transient by definition and so should not stick around
forever. Packages should therefore be automatically expired after some default
time.

* "testing": expire after 2 weeks
* "dev": expire after 4 weeks
* "rc": expire after 8 weeks

**Open Question**: How do we programmtically access the pacakages upload time?
 This is under discussion in the PR for this cfep.


## Alternatives

* Upload testing packages to a different anaconda.org user, perhaps
  `conda-forge-testing`
* Dump all pre-release packages into one label: "pre"
* Additionally label all packages with the feedstock name as a prefix to the
  label, e.g.: "matplotlib-dev"

## FAQ

* What about "alpha" and "beta" labels?

There is not yet heavy use of labels on the conda-forge channel on anaconda.org.
Given the light usage of labels thus far it seems rather unnecessary to
introduce many labels.  "testing", "dev" and "rc" seem like a nice compromise.

* How do I use labels to install these pre-release packages?

Use the following command, but replace `PACKAGE_NAME` with the package you want
to install and replace `LABEL` with "rc", "dev" or "testing":

```
conda install -c conda-forge -c conda-forge/label/LABEL PACKAGE_NAME
```

For example, let's install matplotlib from the "rc" label:

```
conda install -c conda-forge -c conda-forge/label/rc matplotlib
```

As of this writing (01/01/2017), the above command will install matplotlib
version "2.0.0rc2-np111py35_2" which has the "rc" label on the conda-forge
channel on anaconda.org.

## Copyright

This document has been placed in the public domain.
