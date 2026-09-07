
<table>
<tr><td> Title </td><td> Tracking ABI version separately from API version </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Daniel Ching &lt;carterbox@no.reply.github.com&gt;</td></tr>
<tr><td> Created </td><td> Jul 22, 2022</td></tr>
<tr><td> Updated </td><td> Jul 26, 2022</td></tr>
<tr><td> Discussion </td><td>
  https://github.com/conda-forge/conda-forge.github.io/issues/610
  https://github.com/conda-forge/conda-forge.github.io/issues/157
  https://github.com/conda-forge/conda-forge.github.io/issues/150
  https://github.com/conda-forge/conda-forge.github.io/issues/2401
  https://github.com/conda-forge/cfep/pull/48
</td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Conda lacks a built-in method of tracking ABI separately from API. This is fine for python,
whose ABI/API are effectively the same, but for compiled libraries it is not. When
maintaining a package feedstock, determining what ABI compatability guarantees that a
project offers between API releases is cumbersome and often involves attempting to make
contact with upstream maintainers or manually monitoring a package's ABI for changes.
However, projects which care about ABI stability usually already offer this information in
the form of the SONAME which is a compatability marker of the ABI and is commonly added to
the name of the shared library. This proposal aims to formalize the method of separating
versioned libraries into their own output and adding the SONAME to the end of the package
name for conda-forge in order to make maintaining ABI compatability between releases of
packages more automated when that information is available from upstream.

## Motivation

Conda doesn't have a built in way for tracking ABI separately from API which causes hardship
for recipe maintainers in an ecosystem where packages are required to link dynamically. In
order to prevent ABI breaks from a future API release, maintainers need to know ahead of
time at which API release, the ABI will change. This means either asking upstream package
maintainers about compatability guarantees or exporting a pin to the minor version.

The first of these approaches is imperfect because maintainers could change their policy or
not have a policy. The other is inflexible because always pinning to the minor API version
may not be necessary. One approach may lead to broken packages, and the other causes extra
package churn downstream and may occasionally cause unsolvable environments.

## Specification

Recipes for projects which provide separate API and ABI versions should separate the
versioned shared objects into a separate output with the SONAME appended to the output name.
The run_export for this package should have no upper bound.

Recipes for projects that use the same version for API and ABI or for projects that only
have an API vrsion may separate shared objects into a separate output, but should not append
the SONAME to the output name. The run export for this package should be bounded according
to claimed compatability guarantees or to the patch version (`x.x.x`).

## Sample Implementation

When the API is versioned separately from the API:

```yaml
{% set build = 0 %}
{% set version = "0.10.1" %}
# Look in the libavif top level CMakeLists.txt for the updated ABI version.
# ABI is updated separately from API version.
{% set soversion = "14.0.1" %}
{% set soname = soversion.split('.')[0] %}

package:
  name: libavif-sdk
  version: {{ version }}

build:
  number: {{ build }}

requirements:
  build:
    - {{ compiler('c') }}
    - {{ stdlib('c') }}
  host:
    - dav1d-dev
    - ravie-dev

outputs:

- name: libavif-dev
  build:
    run_exports:
      - {{ pin_subpackage("libavif", max_pin=None) }}
      - {{ pin_subpackage("libavif" ~ soname, max_pin=None) }}
  requirements:
    run:
      - {{ pin_subpackage("libavif" ~ soname, exact=True) }}  # [unix]
      - {{ pin_subpackage("libavif", exact=True) }}           # [unix]
  files:
    - lib/libavif.so  # [linux]
    - include/avif.h  # [unix]
    - Library/lib/avif.lib    # [win]
    - Library/include/avif.h  # [win]

# This package acts a mutex. It prevents multiple libavif ABIs from being installed
# simultaneously. It may be excluded, but in most conda environments only one ABI of a
# given library is needed.
- name: libavif
  requirements:
    run:
      - {{ pin_subpackage("libavif" ~ soname, max_pin=None) }}

- name: libavif{{ soname }}
  requirements:
    build:
      - {{ compiler('c') }}
      - {{ stdlib('c') }}
    host:
      - dav1d-dev
      - ravie-dev
  files:
    - lib/libavif.so.{{ soname }}     # [linux]
    - lib/libavif.so.{{ soversion }}  # [linux]
    - Library/bin/avif-{{ soname }}.dll  # [win]

- name: libavif-static
  requirements:
    run:
      - {{ pin_subpackage("libavif-dev", exact=True) }}
  files:
    - lib/libavif.a  # [unix]
    - Library/lib/avif_static.lib  # [win]
```

When ABI compatability is unknown or API and ABI are versioned together:

```yaml
{% set build = 0 %}
{% set version = "0.10.1" %}

package:
  name: libavif-sdk
  version: {{ version }}

build:
  number: {{ build }}

requirements:
  build:
    - {{ compiler('c') }}
    - {{ stdlib('c') }}
  host:
    - dav1d-dev
    - ravie-dev

outputs:

- name: libavif-dev
  build:
    run_exports:
      - {{ pin_subpackage("libavif", max_pin='x.x.x') }}
  requirements:
    run:
      - {{ pin_subpackage("libavif", exact=True) }}
  files:
    - lib/libavif.so
    - include/foo.h

- name: libavif
  requirements:
    build:
      - {{ compiler('c') }}
      - {{ stdlib('c') }}
    host:
      - dav1d-dev
      - ravie-dev
  files:
    - lib/libavif.so.*

- name: libavif-static
  requirements:
    run:
      - {{ pin_subpackage("libavif-dev", exact=True) }}
  files:
    - lib/libavif.a
```

## Rationale

The Debian ecosystem already has solved the problem of needing to know future ABI
compatability by splitting shared library artifacts off from compile-time artifacts into
their own package in the proposed manner. Appending the SONAME to the package name exposes
ABI compatability information to the package solver separately from the API (package)
version and allows downstream packages to delare a dependency on the ABI of a library
separately from the API.

For example, package `bar` which dynamically links to `libfoo` might build against package
`libfoo-dev=2.1.0` which would export a runtime dependency on `libfoo4>=2.1.0` which is the
package which contains `libfoo.so.4`. In this case, an upper bound is not needed for the
run_export because any breaking changes to the ABI would be accompanied by a name
change of the run_export to `libfoo5`.

This approach is implementable today. It does not need conda to explicitly implement a new
ABI tracking feature.

This approach requires no changes from downstream packages except using the latest build.
They will be guarded from ABI breakages as soon as they rebuild with the updated package.

This approach may reduce package churn by allowing for looser channel-wide pinnings since
older builds can coexist with newer builds as long as the ABI of their shared dependency
hasn't changed. For example, the openmpi project has claimed to preserve ABI compatability
across multiple API major versions, so using soname packages may prevent less-frequently
rebuilt packages from holding back more frequently built packages.

This approach reduces feedstock and channel maintenance for projects whose ABI and API
breaks are not correlated but well documented. For example, for projects where performance
improvements are important and ABI breaking like dav1d and libavif sonamed packages
automatically handle ABI compatability.

## Backward Compatability

Transitioning from a single output to multi-output feedstock may cause clobbering between
the pre-split and post-split shared-library package if the run_export from the existing
package is not the same as the run_export from the new package. For example, splitting
`dav1d` (one output) into `dav1d-dev` and `libdav1d` (multi-output) may cause collisions
between downstream packages that depend on `dav1d` and `libdav1d`. To avoid collision,
preserve the exisiting package name as the development package (`dav1d` becomes `dav1d` and
`libdav1d`) or use a combination of channel-wide migrations and repodata patches to migrate
old packages and builds to the new package names.

## Alternatives

### API Pinning Only

In theory, breaking ABI changes can be introduced without changing the API
(reordering struct elements for example). In practice, project managers would
always make a new API release for this change, so ABI breaks can be avoided by
pinning down to the patch version. This is the most conservative approach, but
is the least flexible. Recipe maintainers can pin to minor API versions if the
upstream package makes any such promises about not breaking the ABI.

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

### Including ABI information in the build string

The ABI version could be added to the package build string as custom metadata in the way
that human-readable package variants are implemented. However, this approach may make
matching actual build variants more difficult.

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
