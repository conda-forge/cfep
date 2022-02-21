
<table>
<tr><td> Title </td><td> Providing manually uploaded builds on the conda-forge anaconda channel</td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Hadrien Mary &lt;hadrien.mary@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sep 27, 2016</td></tr>
<tr><td> Updated </td><td> Oct 17, 2019</td></tr>
<tr><td> Updated </td><td> Feb 21, 2022 (hmaarrfk) </td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Some packages, for example Qt and gcc, are very hard to build on the currently used CIs (CircleCI, Travis, AppVeyor, and AzurePipelines) due to time limitation.

While this is a small set of packages we need a protocol to upload builds on conda-forge's channel that are built off-CIs (local builds).

Note that this is a "last resort" option and we should always try to build on CIs first!

## Specification

The protocol we propose is:

- The package needs a feedstock and will be maintained via PRs as any other package;

- If the CI resources (which includes time, memory, disk-space and special hardware) are the only impediments for the build, one should ping the core team in the feedstock and a prepare local build;

- When a PR is approved, someone authorized by a core member, a delagate, will build the package locally, following the [conda-forge docs](https://conda-forge.org/docs/maintainer/updating_pkgs.html#testing-changes-locally) for local builds. Always be sure to check the docs for the latest best practices.
    - If a delegate builds the package, it is recommended they upload it to their own conda channel with a unique label. The label `cfep03` is a good choice.

- The build logs should be saved and posted in the feedstock PR. One easy way to achieve that on Linux is to use `python build-locally.py 2>&1 | tee log.txt` to build.

- Core members can then review the logs and test packages.

- To upload the packages to the conda-forge channel, core members are provided with two options:
    1. Upload the package manually with the appropriate `anaconda upload` command.
    2. Copy the package over from the delegate's channel. If the delegate has provided a unique tag, such as `cfep-03`, the following commands can be used to copy over the packages
```bash
anaconda copy --from-label cfep03 --to-label main --to-owner conda-forge DELEGATES_CHANNEL/PACKAGE_NAME/PACKAGE_VERSION
```

- In both cases, the core member will need to install `anaconda-client` and login with their `anaconda.org` username.

- Note that manual uploads have the uploader username, serving as a marker for manual uploads and a tracker for asking questions regarding the build environment used.

## Motivation

Main motivation is to be able to provide hard to compile/long builds on conda-forge.

## Alternatives

A long term solution would be dedicated machines that we could setup via a CI service, like AzurePipelines.

## FAQ


## Copyright

This document is CC0 1.0 Universal.
