
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

## Specification

Mark the {testing|dev|rc|beta|alpha} package on anaconda.org with two labels

* **matplotlib-testing**: Use the {{ feedstock_name }}-testing tag so that
  users can choose to install **only** that {testing|dev|rc|beta|alpha} 
  package
* **testing**: Use the testing label so that **all** of these packages can
  be easily installed, for the truly bleeding edge.

**Open question**: How do we automatically label these testing builds on the CI
services?

Here are the three options that come to mind:

1. Provide a script that can be run locally to modify the repository scripts to upload
   to **testing** and **NAME-testing**.
2. Create some code that checks the branch name (relevant-branch-name_CFTU) for
   some word or phrase (`_CFTU`) that is short and meaningful
   (CondaForgeTestingUpload) but is also not likely to be accidentally included 
   in a "normal" branch name on the feedstock. (Note, _CFTU is merely a suggestion)
3. Provide the recipe maintainers with documentation showing them how to modify the
   CI scripts to upload the built packages to **testing** and **NAME-testing**

Number 3 above is a distant third for me.  I am quite convinced that we need to
do this in an automated fashion to prevent namespace pollution on anaconda.org/conda-forge, 
especially since removing packages is a labor-intensive process for the core devs.

## Motivation

* Arrive at consensus for how we think that testing builds should be
  denoted on anaconda.org/conda-forge
* Proving sufficient guidance and help to recipe maintainers to prevent the
  conda-forge channel on anaconda.org from becoming an unmaintainable mix
  of "stable" and "testing" packages.
* People are asking for this functionality, so we should figure out how to
  provide it!

## Alternatives

* Upload testing packages to a different anaconda.org user, perhaps
  `conda-forge-testing`
* Tag all testing packages with **testing**
* Tag all testing packages with **{{ feedstock_name }}-testing**

## FAQ

* How do I use labels to install these testing packages?

Let's also assume that we are trying to install the most recent testing package
for matplotlib (and that matplotlib has testing packages labeled on
anaconda.org/conda-forge)


Option 1: Get *just* the matplotlib testing package

```
conda install -c conda-forge/label/matplotlib-testing matplotlib
```

Option 2: Get matplotlib testing and *all* testing packages in the matplotlib
dependency tree

```
conda install -c conda-forge/label/testing matplotlib
```

## Copyright

This document has been placed in the public domain.
