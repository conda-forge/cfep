
<table>
<tr><td> Title </td><td> Providing manually uploaded builds on the conda-forge anaconda channel</td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Hadrien Mary &lt;hadrien.mary@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sep 27, 2016</td></tr>
<tr><td> Updated </td><td> Oct 17, 2019</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Some packages, for example Qt and gcc, are very hard to build on the currently used CIs (CircleCI, Travis, AppVeyor, and AzurePipelines) due to time limitation.

While the is a small set of packages we need a protocol to upload builds on conda-forge anaconda channel that are built off-CIs (local builds).

## Specification

The protocol we propose is:

- The package needs a feedstock will be maintained via PRs as any other package;

- When a PR is approved a core member will build the package locally, following the [conda-forge docs](https://conda-forge.org/docs/maintainer/updating_pkgs.html#testing-changes-locally) for local builds. Always be sure to check the docs for the latest best practices.

- Core members can then test and upload the packages. Not that manual uploads have the uploader username, serving as a maker for manual uploads and a tracker for asking questions regarding the build environment used.

## Motivation

Main motivation is to be able to provide hard to compile/long builds on conda-forge.

## Alternatives

A long term solution would be dedicated machines that we could setup via a CI service, like AzurePipelines.

## FAQ


## Copyright

This document is CC0 1.0 Universal.