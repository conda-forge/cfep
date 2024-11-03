
<table>
<tr><td> Title </td><td> Globally Pinned Minimum Python Version for `noarch: python` Packages </td>
<tr><td> Status </td><td> Accepted </td></tr>
<tr><td> Author(s) </td><td> Matthew R. Becker &lt;becker.mr@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Oct 12, 2024</td></tr>
<tr><td> Updated </td><td> Nov 3, 2024</td></tr>
<tr><td> Discussion </td><td> #56 </td></tr>
<tr><td> Implementation </td><td> <a href="https://github.com/conda-forge/conda-forge.github.io/issues/2351">conda-forge/conda-forge.github.io/issues/2351</a> </td></tr>
</table>

## Abstract

This CFEP proposes a mechanism to globally pin a minimum Python version for `noarch: python` packages.

## Background

A frequent issue observed with `noarch: python` packages has been lower bounds that are not being updated when the upstream package drops support for a Python version. As a result, the `noarch: python` package continues to be built and installed for Python versions the package no longer officially supports. Sometimes users get lucky and no issues occur. Other times pulling in the package breaks an older Python install they are using. The result is work by users, maintainers, and core to fix the package metadata and patch the repodata. Eating into time that might be better spent by all on other issues.

To minimize this form of breakage, this CFEP sets forward a new practice to ensure `noarch: python` packages **always** test their minimum supported version. This will hopefully identify issues with the minimum version in the feedstock CI instead of consumers environments. 

## Specification

It relies on a new value in the global pinnings file, `python_min`, that specifies the current oldest supported version of Python. Packages that are `noarch: python` will be built and tested against this oldest version of Python, but allowed to run on any newer Python version. Maintainers can override these values and rules locally according to their package's requirements if needed. Through this mechanism, we will ensure that as Python versions are no longer supported, we stop building packages that can run on them, avoiding potential incompatibilities for older environments.

A new value, `python_min`, will be added to the global pinnings file in `conda-forge/conda-forge-pinning-feedstock`. Its value will be the `major.minor` version of the oldest supported Python version (e.g., `3.9`, `3.11`, etc.). Recipes that create `noarch: python` packages will use the following configuration in their build definitions (e.g., `meta.yaml`, `recipe.yaml`, etc.):

```yaml
host:
  - python {{ python_min }}.*
run:
  - python >={{ python_min }}

# for recipe.yaml, the test section below is different
# but equivalent constraints can be added
test:
  requires:
    - python ={{ python_min }}
```

Maintainers may override the minimum Python version `python_min` in `recipe/conda_build_config.yaml` or change the build configuration as needed to match the constraints of their specific package.

However, we strongly recommend that maintainers always test their package against the oldest Python version for which it would install in order to ensure compatibility.

## Rationale

The procedure above was discussed extensively in issue [`#2210`](https://github.com/conda-forge/conda-forge.github.io/issues/2210). The rationale is as follows:

- Using a global pinning value allows us to easily update the minimum Python version for all `noarch: python` packages at once, either through a migration or by simply changing the value and letting the ecosystem update over time.
- While `noarch: python` packages may be incompatible with either newer or older Python versions, we judged that testing against the oldest Python version would catch more issues than the other way around.
- The oldest supported Python version is specified as a `major.minor` version, meaning that packages built against, for example, Python `3.9.1` can be installed in environments with Python version that have lower patch versions (e.g., `3.9.0`). For `noarch: python` packages, we do not expect the patch version to matter since there should be no breaking changes in Python syntax for patch version bumps.
- The name `python_min` was chosen to match the convention taken for the minimum CUDA compiler version (i.e., `cuda_compiler_version_min` for the pin value `cuda_compiler_version`).

## Alternatives

We had extensive discussions on the alternative of always pinning the minimum Python version to that specified in the upstream source (if given). However, we chose not to do this since `conda-forge` is a separate ecosystem, and we don't feel comfortable shipping new package versions that run on Python versions that we no longer support.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
