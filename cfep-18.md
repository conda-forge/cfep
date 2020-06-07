
<table>
<tr><td> Title </td><td> Uploading binaries from pull requests </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Chris Burr &lt;christopher.burr@cern.ch&gt;</td></tr>
<tr><td> Created </td><td> June 06, 2020</td></tr>
<tr><td> Updated </td><td> June 06, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Allow package binaries to be uploaded from builds in pull requests.

## Motivation

Sometimes it would be useful to access binaries built in pull requests. Reasons include:

* Testing GUI applications
* More easily testing the effects on a pull requests on downstream packages. 
  For example, patches in llvm affecting builds of clang.
* Debugging non-obvious failures during the tests stage of the build

While it is always possible to build binaries locally it can difficult to set up (e.g. macOS/Windows) 
or time consuming people only have access to less powerful machines.

## Specification

Upon request, any binaries built in a PR will be uploaded to a file sharing service. Any file sharing service will
is allowed. However, [Firefox Send](https://github.com/timvisee/ffsend) appears to be a good default choice. Other 
potential services are 

 - [https://transfer.sh/](https://transfer.sh/) 
 - a self-hosted solution such as [transfer.sh](https://github.com/dutchcoders/transfer.sh)
 - [0x0](https://github.com/mia-0/0x0).

There are a few additional requirements.

 - A user should be able to enable this feature without rerendering the PR in order to enable easy debugging.
 - This feature will automatically be disabled upon a rerender to avoid extra uploads.
 - Packages should be uploaded with the repo data regardless of whether or not the tests pass.
 - After the file is uploaded the URL will be shown in the CI log alongside a secure hash to 
   allow the file integrity to be verified.
 - Files can be deleted after some nominal time period (of order a few days).

## Rationale

While this feature is useful in some situations, most binaries will never be downloaded and as such 
should not be uploaded by default to avoid wasting resources. Disabling this feature after each 
re-render minimises the chances that this feature will be accidentally left enabled.

Firefox Send is proposed for the initial service provider due to it being provided by a large and 
well-known organization.

## Alternatives

The use of a dedicated channel would make it slightly easier to use the uploaded binaries however this proposal was rejected for the following reasons:

  * This would likely be hosted on anaconda.org increasing the costs incurred by Anaconda, Inc.
  * Uploading to a channel would require credentials to be shared with pull requests and would be 
    almost impossible to do securely
  * Additional infrastructure would be required to automatically delete packages after a short period of time
  * Identical build hashes in different builds can not be uploaded without modification
  * Packages from different pull requests/feedstocks may interact, or would require a large number 
    labels to isolate from each other

The additional complexity from installing a binary instead of providing a full channel can be managed 
by adding documentation to [https://conda-forge.org/docs](https://conda-forge.org/docs).

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).

