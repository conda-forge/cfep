
<table>
<tr><td> Title </td><td> Standard package split (dev / doc / dbg) </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Sylvain Corlay &lt;sylvain.corlay@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Oct 21, 2020</td></tr>
<tr><td> Modified </td><td> Oct 22, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This CFEP proposes a policy on how to split packages in multiple outputs for

 - the main runtime requirements (shared objects, executables)
 - the developer requirements (headers, static libraries, pkg-config and cmake artifacts)
 - the requirements for debugging (debug symbols, JS map files)
 - documentation.

Notes:

 - The Debian community follows a convention similar to this proposal.
 - Should this CFEP be accepted, it would supersede [CFEP 18](https://github.com/conda-forge/cfep/blob/master/cfep-18.md) on the split of static libraries.
 - This CFEP will be much easier to execute upon if we improve the conda tooling around the support for multiple outputs for recipes.

## Motivation

At the moment, there is not a common pattern to split packages in multiple output including C/C++ headers, JS source maps, or artifacts of build systems that are not required at runtime but are necessary at build time. Packages have been split in various ways, but adopting a convention will allow us to create tooling, idioms, and best practices on how to deal with such split packages.

Expected benefits of this CFEP are

 - An explicit convention on how to split packages. (There is an increasing number of packages split in various ways in conda-forge, but we lack idioms and patterns to apply).
 - A finer-grained control over run exports of the different packages, most importantly for header-only and stacic libraries.
 - A convention on how to package debug symbols (for libraries and executables) and JavaScript sourcemaps (for applications shipping a JavaScript bundle). Sourcemaps tend to weigh several megabytes and should not be included in the base package if possible.

## Output names and what goes where

The proposal is to split most packages in several outputs. For example, package `foobar` may be split into

 - `foobar`
 - `foobar-dev`
 - `foobar-dbg`
 - `foobar-doc`

### `foobar`, the main runtime package

The `foobar` package should only contain the parts of the package that are needed at runtime, such as 

 - Shared objects
 - Executables
 - Other assets needed at runtime to use the package

It may not include headers, static libraries, debug symbols, JavaScript source maps, or artifacts from the build systems such as pkg-config or cmake. These exclusions may be checked in CI.

### `foobar-dev`, the development package

The `foobar-dev` should contain headers, build system artifacts, and possible static libraries.

It may not include shared objects or executables, or any other content required at runtime by the `foobar` package.
Also, debug symbols and source maps should be in the `foobar-dbg` package.

### `foobar-dbg`, the debug package

The `foobar-dbg` should contain the debug symbols, or other similar artifacts, such as JS sourcemaps, which are not required at runtime or for building on top of the package, but are needed to troubleshoot issues with the package at runtime.

It may not include any of the content of the `foobar` or `foobar-dev` packages. The `foobar-dbg` package is really a companion package to `foobar`, and should not e.g. include a debug build of the same package.

### `foobar-doc`, the documentation package

The `foobar-doc` package contains built documentation for `foobar`.

### Special cases

Some packages may only have a `-dev` package and no runtime package, such as header-only libraries, or libraries that do not produce a shared object.

## Dependencies and exports

### Dependencies between `foobar`, `foobar-dev`, and `foobar-dbg`

 - We expect `foobar-dbg` to always list `foobar` as a run dependency, pinned to the same version.
 - However, `foobar-doc` does not require `foobar`.
 - Also, `foobar-dev` will typically depend on `foobar` (if it exists), pinned to the same version.

Note: while we may expect usecases where for e.g. statically linking with libfoobar, we only need foobar-dev, it is probably a good pattern to have foobar-dev always depend on foobar to not break expectations.

### Depending on `-dev` packages

 - Most typically, the only packages to have a `-dev` package as a run dependency will be other dev packages, and compilers.

### Exports

 - If `foobar` includes a shared object, it will most typically have a run export for itself, with the binary compatible version.
 - Similarly, `foobar-dev` may export a `run_constraints` for itself. (`run_constraints_exports` is not currently a feature of conda-build but the idea is for it to behave similarly to `run_exports`, but by adding `run_constraints` instead of `run` dependencies).

### Empty runtime packages?

In the case of a header-only library, or a library that does not produce a shared object, we may not have any content for the runtime package. However, it may be a good thing to keep such as **empty output**, which will be a `run_constraints_export` of `foobar-dev` as detailed in the previous section.

This practice will ensure compatibility constraints when linking packages built with different versions of a header-only library.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
