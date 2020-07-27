
<table>
<tr><td> Title </td><td> Pinning Epochs </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Matthew R. Becker &lt;becker.mr@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Jul 26, 2020</td></tr>
<tr><td> Updated </td><td> Jul 26, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

`conda-forge` will support *pinning epochs* (PE) by having one or more additional pinning files
that define PE versions. These pinning files will be used by `conda-smithy` to effectively
backport the current versions of downstream packages to the older PE pins. This backporting will be
done on an *opt-in* basis by setting an appropriate setting in the `conda-forge.yml` on the feedstock.
When set, this setting will instruct `conda smithy` to render the feedstock with both the PE pinning file and
the latest pins. PE metapackages will be made so that users can enforce a single global pinnings version on
their environments.

## Motivation

Currently, `conda-forge` only builds the latest version of each package with the latest
global pinning file. Every user of `conda-forge` is therefore always at the bleeding edge.
Some users, especially larger organizations which depend on the effective `conda-forge` ABI
for their own compiled software, may need a more stable set of pinnings and packages.
This CFEP is a proposal to create pinning epochs in `conda-forge` in order to help these users.

## Specification

 - `conda-forge` will define a "pinning epoch" as the pinnings file from some past version of the
   global pinnings file.
 - The version number of the pinning epoch will be `YYYY.MM` from the pinning file it corresponds
   to (e.g., `2020.06` for a pinning file from June of 2020).
 - Any pinning epochs files will be duplicated in the pinnings repo, installed into the miniconda/miniforge
   in the same way as the usual pinnings file, and given a descriptive name.
 - Core can at it discretion copy keys from the latest pinnings file to a PE epoch in order to eliminate
   unnecessary rebuilds. Examples of these keys could include, but are not limited to, forward-compatible pins (i.e., corresponding
   to constraints like `>=9`), things like `docker_image`, and big deprecations like a version of `python` or `numpy`.
 - If instructed via an opt-in setting in the `conda-forge.yml`, `conda-smithy` will generate builds of a
   given package against both the bleeding edge pinning file and the pinning epoch file(s). Migration YAML files
   will not be applied to the PE files when generating builds.
 - Packages in the pinnings epoch files are excluded from these extra builds except as needed to satisfy
   the pinning file itself.
 - Individual feedstock maintainers must opt-in to PE builds through setting a key in the `conda-forge.yml` file.
 - The core dev team will aim to declare pinning epochs with a frequency of at most ~6 months. Only at most two pinning
   epochs are allowed at any one time.
 - Each pinning epoch will come with a metapackage that has the pins as run constraints to help keep
   environments on a single set of pinnings. This packages will be called `conda-forge-release` with
   the version of the pinning epoch.

## Rationale

 - By using a full pinnings file, we capture all of the relevant build information, including
   common dependencies (e.g., `boost`), the compiler versions, the set of source channels, etc.
 - Having PE versions being built on the `master` branch of feedstocks solves quite a few issues.
     - We avoid the burden of maintainers having to backport bug fixes to other branches.
     - We get version update PRs from the autotick bot for free.
     - We can reduce build duplication by combining common variants across PE versions.
 - Not building packages listed in the pinnings file against the PE pinning variations versions
   themselves avoids large expansions of build matrices.
 - Effectively zipping all of the keys in each version of the pinnings files also avoids large expansions in
   the build matrices.
 - By making the PE pinning a specific version of the global pinnings file, we can avoid significant
   numbers of extra builds. Packages that are needed/effected by the LTS pinnings will be (re)built naturally
   as the autotick bot creates pinning migrations and issues new version PRs.
 - This approach is user-friendly and matches our established maintenance idioms. Should a build needed for
   a PE version not exist, a simple rerender is all that is needed to generate the appropriate changes
   to the feedstock. This rerender can be made by any github user and the resulting PR can be merged to master
   with the user adding themselves as a maintainer in order to help keep the feedstock up to date.

## A Worked Example

Suppose the commits to the pinnings file (in reverse chronological order, so most recent first) look like this

| commit | boost | icu  | pinning epoch | foo |
| ------ | ----- | ---- | ------------- | --- |
| y      | 1.72  | 0.67 |  latest       | v2  |
| a      | 1.72  | 0.62 |  e2           | v2  |
| b      | 1.70  | 0.62 |  -            | v1  |
| c      | 1.70  | 0.58 |  e1           | v1  |

Note that I have marked two pinning epochs (`e1`, `e2`) and the `latest` pinning version.
(Assume no migrations are going, but this doesn't actually matter since that only effects `latest`).
I've also marked chronologically when `foo` versions `1` and `2` were released and thus which
pinning versions they were built with.

In our standard scheme, what versions we would have available to us are just those in the table above.

  - foo v1 w/ pinning commit c (boost 1.70, icu 0.58)
  - foo v1 w/ pinning commit b (boost 1.70, icu 0.62)
  - foo v2 w/ pinning commit a (boost 1.72, icu 0.62)
  - foo v2 w/ pinning commit y (boost 1.72, icu 0.67)

In other words, `conda-forge` always lives on latest of whatever is in the pinnings and `foo`'s feedstock.

With this pinning epoch CFEP, `foo` v2 (the current version on master in the feedstock)
would be built on the pinning epochs `latest`, `e1` and `e2`. Thus we would add one extra build

  - foo v2 w/ pinning commit c (boost 1.70, icu 0.58)

The idea is that the latest version of any feedstock that opts-in is always built on the most recent
two active pinning epochs to the extent that is possible.

## FAQ

1. Q: What if we have pinning epoch `e1` with say `boost` `1.70` and the current global pin for `latest` is
   `boost` `1.72`. Would we ever build `boost` `1.71`? What is the rationale for not building it?

   A: We would not build `boost` `1.71`. The rationale of this CFEP is that the pinning epochs come from a
      previously existing set of pins on `conda-forg`. This procedure eliminates extra work and builds against
      versions of pinned dependencies that we never built against in the first place. This limitation is key to
      decreasing the amount of extra work our maintainers might face.

2. Q: What if a pinning epoch results in extra builds beyond the bare minimum a user might need? For example,
      suppose a pinning epoch has the older version of `boost` but also generates variants for a more bespoke
      pinned package that has few downstream dependencies. Should we make an effort to eliminate these extra
      variants?

   A: No, we should not. Eliminating the extra variants introduces too much complexity into how a pinning epoch
      is defined. The variants might be extra for one user, but could be needed for another, for example. By
      requiring a pinning epoch to correspond exactly to a full pinnings file, we can generate consistent expectations
      for users.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/)
