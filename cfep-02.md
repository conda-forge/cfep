
<table>
<tr><td> Title </td><td> Upgrade default macOS Travis image to `xcode6.4` </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Andreas Kloeckner &lt;inform@tiker.net&gt;</td></tr>
<tr><td> Created </td><td> Nov 8, 2016</td></tr>
<tr><td> Updated </td><td> Nov 8, 2016</td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/conda-forge-enhancement-proposals/pull/6 </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Upgrade the default Travis image to `xcode6.4`.

## Implementation

This is a one-line change to `.travis.yml`:

```
osx_image: xcode6.4
```

*   Adapt `conda smithy` so that it applies that edit on rerender.
*   Apply the change in the `staged-recipes` master branch.
*   Rebuild all packages with this? (TBD? Opinions?)
*   Suggest that all packages include the `toolchain` so that
    the macOS 10.9 deployment target can be set.

## Rationale

*   The current default image (`beta-xcode6.1`) is deprecated
    and will cease to be supported/provided by Travis [@jakirkham: citation needed],
    so this change will have to happen at some point anyway.

*   We won't be able to compile [clang
    3.8/3.9](https://github.com/conda-forge/staged-recipes/pull/1481) without
    this change.

## Backward Compatibility

*   The default deployment target for `xcode6.4` is macOS 10.11.
    It would need to be forced to 10.9 (the lowest supported one
    for that image) by hand.

*   Even with this change, this would implicitly drop support for
    macOS 10.7 and 10.8.

## References

* [@jakirkham on implications][impl]
* [Travis image support policy][travis]

[impl](https://github.com/conda-forge/conda-forge.github.io/issues/249#issuecomment-256207392)
[travis][https://github.com/travis-ci/travis-ci/issues/6765#issuecomment-256703076]

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
