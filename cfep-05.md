
<table>
<tr><td> Title </td><td> Providing pre-release builds on the conda-forge anaconda channel </td>
<tr><td> Status </td><td> Accepted </td></tr>
<tr><td> Author(s) </td><td> Eric Dill &lt;thedizzle@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sept 09, 2016</td></tr>
<tr><td> Updated </td><td> Feb 5, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

There are a number of recipe maintainers who have asked for guidance regarding
how to make pre-release builds available with the CI infrastructure on
conda-forge. This document outlines how those builds should be labeled on
anaconda.org so that the more adventurous users can try out these pre-release
packages.

## Motivation

PyPI allows package maintainers to upload "prerelease" builds which users can
then install via

```
pip install PACKAGE_NAME --pre
```

The same semantics are slightly more cumbersome within the Conda ecosystem but
are still manageable.  The analogous command with `conda` is (for example):

```
conda install -c conda-forge/label/matplotlib_rc matplotlib
```

We, as the conda-forge community need to arrive at a consensus for what labels
we will affix to packages so that there is a consistent user experience.

## Backwards Compatibility

There are only minimal concerns of backwards compatibility.
The number of feedstocks that issue pre-releases is small, mitigating
backwards compatibility issues.

## Specification

Mark the pre-release package on anaconda.org as "dev" or "rc" by adding them to 
the appropriate label.
The semantics of these labels should generally follow the
[guidelines](https://docs.python.org/devguide/devcycle.html#stages) that Python
itself follows.

- "rc": [Beta](https://docs.python.org/devguide/devcycle.html#beta) and [Release
  Candidate](https://docs.python.org/devguide/devcycle.html#release-candidate-rc)
  (RC). No new features. Bugfix only.

- "dev": [Pre-Alpha](https://docs.python.org/devguide/devcycle.html#pre-alpha)
  and [Alpha](https://docs.python.org/devguide/devcycle.html#alpha). These are
  still packages that could see substantial changes
  between the dev version and the final release.

See the Appendix for dev and rc version order specifics.

To create a `dev` or `rc` package a PR can be issued into the `dev` or `rc` branch of the
feedstock.
This branch must change the `recipe/conda_build_config.yaml` to point to the `<package_name>_dev` or `<package_name>_rc` label.

For example, matplotlib rc releases would include:
```yaml
...
channel_targets:
  - conda-forge matplotlib_rc
```

If a pre-release build of B depends on a pre-release build of A, then A should have,
```yaml
...
channel_targets:
  - conda-forge A_rc
```
while B should have,
```yaml
...
channel_sources:
  - conda-forge/label/A_rc,conda-forge,defaults
channel_targets:
  - conda-forge B_rc
```
in `recipe/conda_build_config.yaml` in their respective feedstocks.

NOTE: A rerender needs to happen for these changes to reflect in CI files.

## Exceptions

Certain packages (for example [black](https://pypi.org/project/black/#history)) follows
a release cycle in which they have never had a non-beta/alpha release.  In these cases
the conda packages for those do *not* need to be published to a prerelease label.

Once a non-prerelease version of such a package is available and has been merged into the associated feedstock they lose this exception and have to publish prereleases to a prerelease label like other packages.


## Alternatives

* Upload pre-release packages to a different anaconda.org user, perhaps
  `conda-forge-pre`
* Dump all pre-release packages into one label: "pre"

We understand the proposed approach does not have the same user experience
as `pip --pre`, which seems simpler and easier to use.
Currently this level of functionality is not possible with conda while
maintaining the stability of environment we currently provide.
This CFEP proposes and initial step toward supporting dev/rc releases.
As we explore this functionality more we can re-evaluate the user and maintainer
experience and how to best provide this functionality.

## FAQ

* What about "alpha" and "beta" labels?

There is not yet heavy use of labels on the conda-forge channel on anaconda.org.
Given the light usage of labels thus far it seems rather unnecessary to
introduce many labels.  "dev" and "rc" seem like a nice compromise.

* How do I use labels to install these pre-release packages?

Use the following command, but replace `PACKAGE_NAME` with the package you want
to install and replace `LABEL` with "rc" or "dev":

```
conda install -c conda-forge/label/PACKAGE_NAME_LABEL -c conda-forge PACKAGE_NAME
```

For example, let's install matplotlib from the "rc" label:

```
conda install -c conda-forge/label/matplotlib_rc -c conda-forge matplotlib
```

## Work Items

- Fix intructions to install the package in the README.

  Currently, conda-smithy generates incorrect instructions in the README.
  This is tracked in https://github.com/conda-forge/conda-smithy/issues/996

- Disable uploading to conda-forge/label/main if sources include other pre-release labels

  This is tracked in https://github.com/conda-forge/conda-forge-ci-setup-feedstock/issues/66

## Updates

- Feb 5, 2020: The labels were changed to put the dev/rc indication after the
  package name rather than before, i.e., `<package_name>_dev` and
  `<package_name>_rc` instead of `dev_<package_name>` and `rc_<package_name>`,
  respectively. This aligns with the conclusions of the survey and discussion
  during the approval process for this CFEP.

## Appendix

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


## Copyright

This document has been placed in the public domain.
