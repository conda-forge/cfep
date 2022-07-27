
<table>
<tr><td> Title </td><td> ABI pinning via SONAME in build string </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Daniel Ching &lt;carterbox@no.reply.github.com&gt;</td></tr>
<tr><td> Created </td><td> Jul 22, 2022</td></tr>
<tr><td> Updated </td><td> Jul 26, 2022</td></tr>
<tr><td> Discussion </td><td>
  https://github.com/conda-forge/conda-forge.github.io/issues/610
  https://github.com/conda-forge/conda-forge.github.io/issues/157
  https://github.com/conda-forge/conda-forge.github.io/issues/150
</td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Conda lacks a built-in method of tracking ABI separately from API. This is fine
for python, whose ABI/API are effectively the same, but for compiled libraries
it is not. Trying to figure out what ABI compatability guarantees that a
project offers between API releases is annoying because you have to contact
upstream maintainers and manually monitor for changes. However, projects which
care about ABI stability already offer this information in the form of the
SONAME which is the version of the ABI and is commonly added to the name of the
shared library. This proposal is a standard procedure for adding the SONAME
into the build string, so that the exisiting conda solver can choose a
compatible ABI automatically.

## Motivation

Conda doesn't have a built in way for tracking ABI separately from API which
causes hardship for recipe maintainers in an ecosystem where packages are
required to link dynamically. In order to prevent ABI breaks from a future API
release, maintainers need to know ahead of time at which API release, the ABI
will change. This means either asking upstream package maintainers about
compatability guarantees or exporting a pin to the minor version.

One of the approaches is imperfect because maintainers could change their
policy or not have a policy. The other is inflexible because always pinning to
the minor API version may not be necessary. One approach may lead to broken
packages, and the other causes extra downstream builds and may occasionally
cause unsolvable environments.

## Specification

Recipes whose libraries include a separate ABI version should add the major ABI
version to the build string between `v` and `so`. For example, `v2so` for
version `2`. This substring in the build string should be used in the
run_export to constrain the pinning to the same ABI major version in addition
to cosntraints on the API version.

Recipes whose libraries have an API version only should export a pin to the
patch level (`x.x.x`) in order to prevent ABI breaks.

Recipes whose package version is the same as the ABI version do not need to
modify their build string.

Recipes with multiple library artifacts whose ABIs are tracked separately
should split these libraries into separate outputs of one recipe. A metapackage
may be used for installation convenience, but the metapackge must also
enumerate the run_exports for each subpackage.

## Sample Implementation

```yaml
{% set name = "libavif" %}
{% set build = 0 %}
# NOTE: Humans must also update the library version in the tests section of this recipe
{% set version = "0.10.1" %}
# Look in the libavif top level CMakeLists.txt for the updated ABI version.
# ABI is updated separately from API version.
{% set so_version = "14.0.1" %}
{% set so_name_major = so_version.split('.')[0] %}

package:
  name: {{ name|lower }}
  version: {{ version }}

build:
  number: {{ build }}
  string: "v{{ so_name_major }}soh{{ PKG_HASH }}_{{ build }}"
  run_exports:
    - {{ pin_subpackage(name|lower) }} *v{{ so_name_major }}so*

test:
  commands:
    - test -f ${PREFIX}/lib/libavif.so.{{ so_name_major }}  # [linux]
    - test -f ${PREFIX}/lib/libavif.so.{{ so_version }}     # [linux]

```

## Rationale

Adding the ABI version to the build string allows the run_export to handle both
API and ABI using existing conda capabilities. Not needing new conda features
saves engineering effort and is implementable today. Build strings are already
used to track features such as the CUDA version and MPI variants.

The ABI version is sandwiched between `v` and `so` because these letters are
not part of hexadecimal and to prevent matching collisions with other build
string tags or higher numbers. i.e. `6*` matches with `60`, but `6so*` does
not.

Using a combination of build string and run_exports to add ABI information
requires no changes from downstream packages except using the latest build.
They will be guarded from ABI breakages as soon as they rebuild with the
updated package.

## Backward Compatability

This proposal does not break exisiting packages. It is a feature that only
helps future package builds.


## Alternatives

### API Pinning Only

In theory, breaking ABI changes can be introduced without changing the API
(reordering struct elements for example). In practice, project managers would
always make a new API release for this change, so ABI breaks can be avoided by
pinning down to the patch version. This is the most conservative approach, but
is the least flexible. Recipe maintainers can pin to minor API versions if the
upstream package makes any such promises about not breaking the ABI.

### New output with ABI in package name

Proposed in [this
issue](https://github.com/conda-forge/conda-forge.github.io/issues/157), this
alternative renames outputs to `{{ name|lower ~ so_name_major }}`. This method
is currently utilized for
[libgfortran](https://github.com/conda-forge/ctng-compilers-feedstock/blob/main/recipe/meta.yaml#L542)
and is common in official linux distributions. It also enables installing
multiple ABI versions of a library simultaneously (remember to refactor to
prevent clobbering). The downside of this alternative is that it requires
multiple recipe outputs which is more difficult to implement).

```yaml
{% set name = "libavif" %}
{% set build = 0 %}
# NOTE: Humans must also update the library version in the tests section of this recipe
{% set version = "0.10.1" %}
# Look in the libavif top level CMakeLists.txt for the updated ABI version.
# ABI is updated separately from API version.
{% set so_version = "14.0.1" %}
{% set so_name_major = so_version.split('.')[0] %}

package:
  name: {{ name|lower ~ so_name_major }}
  version: {{ version }}

build:
  number: {{ build }}
  run_exports:
    - {{ pin_subpackage(name|lower ~ so_name_major) }}

test:
  commands:
    - test -f ${PREFIX}/lib/libavif.so.{{ so_name_major }}  # [linux]
    - test -f ${PREFIX}/lib/libavif.so.{{ so_version }}     # [linux]

outputs:
  - {{ name|lower ~ so_name_major }}
  # This metapackage allows recipes to continue using the unversioned name
  - {{ name|lower }}
    build:
      run_exports:
        - {{ pin_subpackage(name|lower ~ so_name_major) }}
    requirements:
      run:
        - {{ name|lower ~ so_name_major }}
```

### Prepending ABI to package version

This would mean versioning packages to `{{so_name_major}}.{{version}}`. This
approach may not be backward compatible with already published package versions
if the ABI version is lower than the API version and would require migrating
all downstream feedstocks.

### Exporting a separate ABI package

The conda-forge pybind11 package does this currently. An empty package called
pybind11_abi tracks the ABI version and is used as a run_constraint and
run_export. This approach is backward compatible, but requires additions to the
recipe (additional outputs, run_constraints, and exports).

### Modifying conda to automatically track ABI via SONAME

This approach requires new software features on conda instead of relying on
existing features.

# Reference

https://github.com/conda-forge/conda-forge.github.io/issues/610

https://github.com/conda-forge/conda-forge.github.io/issues/157

https://github.com/conda-forge/conda-forge.github.io/issues/150

https://github.com/conda-forge/libavif-feedstock/pull/1#issuecomment-986310764

https://github.com/conda-forge/dav1d-feedstock/pull/1/files#r927851556

https://github.com/conda-forge/libwebp-feedstock/blob/615b5309a76ac96409394aa100ec11bb1c7ea150/recipe/meta.yaml#L14

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
