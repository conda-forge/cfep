
<table>
<tr><td> Title </td><td> Upgrade default macOS Travis image to `xcode6.4` </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Andreas Kloeckner &lt;inform@tiker.net&gt;</td></tr>
<tr><td> Created </td><td> Nov 8, 2016</td></tr>
<tr><td> Updated </td><td> Nov 8, 2016</td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/conda-forge-enhancement-proposals/pull/6 </td></tr>
<tr><td> Implementation </td><td> (listed below) </td></tr>
</table>

## Abstract

Upgrade the default Travis image to `xcode6.4`.

## Implementation

*   Adapt `conda smithy` so that it applies that edit on rerender.
    [One-liner change here](https://github.com/conda-forge/conda-smithy/blob/b1d1730c4b8c8dc849464b1b39569b0e61ec3130/conda_smithy/templates/travis.yml.tmpl#L12)
*   Apply the change in the `staged-recipes` master branch.
    [One-liner change here](https://github.com/conda-forge/staged-recipes/blob/3ce42e083cebe5f39eea82d069d5a94cb7719218/.travis.yml#L5)

    [PR](https://github.com/conda-forge/staged-recipes/pull/1094)
*   Rebuild all packages with this? (TBD? Opinions?)

    Likely not necessary given [C++ ABI Compatibility][cxxabi].
*   Suggest that all new packages include the `toolchain` so that
    the macOS 10.9 deployment target can be set.

    Ideally, the correct setting of the deployment target would be verified by
    CI, but the implementation of such a measure, while
    [technically possible](http://stackoverflow.com/a/17148833),
    is not part of this proposal.

## Rationale

*   The current default image (`beta-xcode6.1`) is deprecated
    and will cease to be [supported/provided by Travis][travis],
    so this change will have to happen at some point anyway.

    It will go away on January 21, 2017 according to the stated
    plan in the linked comment. At that point, all packages
    specifying the previous image (`beta-xcode6.1`) will
    implicitly be bumped to Travis's default image, which will result
    in packages that do not meet conda-forge's promise of OS compatibility
    back to 10.9.

*   Multiple packages need this to move forward:

    *   [Apache Arrow](https://github.com/conda-forge/conda-forge.github.io/issues/249#issuecomment-254331475)
    *   [libdynd](https://github.com/conda-forge/staged-recipes/pull/1051#issuecomment-233279062)
    *   [clang 3.8/3.9](https://github.com/conda-forge/staged-recipes/pull/1481)

## Backward Compatibility

*   The default deployment target for `xcode6.4` is macOS 10.10.
    It would need to be forced to 10.9 (the lowest supported one
    for that image) by hand.

*   The proposed change will not drop support for any previously
    supported OS versions.

## References

* [@jakirkham on implications][impl]
* [Travis image support policy][travis]
* [Travis CI changes default to Mac OS 10.11 XCode 7.3]( https://blog.travis-ci.com/2016-10-04-osx-73-default-image-live/ )
* [Travis CI image restructuring]( https://blog.travis-ci.com/2016-09-15-new-default-osx-image-coming/ )
* [Currently available Travis CI OS X images]( https://docs.travis-ci.com/user/osx-ci-environment/#OS-X-Version )
* [Details on Mac OS 10.9 XCode 6.1]( https://docs.travis-ci.com/user/osx-ci-environment/#Xcode-6.1 ) supports 10.9 and 10.10
* [Details on Mac OS 10.10 XCode 6.4]( https://docs.travis-ci.com/user/osx-ci-environment/#Xcode-6.4 ) supports 10.9 and 10.10
* [Setting OS X deployment target](
https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/cross_development/Configuring/configuring.html
)
* [Apple C++ ABI Compatibility][cxxabi]

[impl]: https://github.com/conda-forge/conda-forge.github.io/issues/249#issuecomment-256207392
[travis]: https://github.com/travis-ci/travis-ci/issues/6765#issuecomment-256703076
[cxxabi]: https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/CppRuntimeEnv/Articles/CPPROverview.html

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
