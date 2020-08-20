
<table>
<tr><td> Title </td><td> Packaging static libraries </td>
<tr><td> Status </td><td> Accepted </td></tr>
<tr><td> Author(s) </td><td> Uwe L. Korn &lt;cfep@uwekorn.com&gt;</td></tr>
<tr><td> Created </td><td> Jun 24, 2020</td></tr>
<tr><td> Updated </td><td> Aug 20, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

This CFEP proposes a policy on how to package static libraries / archives.
These should be separated out into packages that are not installed in a default user setup.
In general, we prefer our packages to work completely without static linkage between different packages but are aware that static archives are sometimes needed / useful.

This CFEP excludes languages like Rust, Haskell, Go where binaries are always statically linked.
The mentioned downsides apply there too but there isn't the option of using dynamic linkage.

## Motivation

At the moment, most binary packages that include libraries only ship their shared version.
Some packages though also include the static version or only ship the static version.
At runtime the static archives are not needed and thus are wasted space to the end user.
In addition, our tooling (conda/conda-build/the bots) is also geared towards maintaining a set of dynamically linked packages.

## Rationale

The current stack used to manage and consume conda-forge is tuned to deal with dynamic linkage between packages.
As long as the solver can find a solution, updating a package version is simply updating the package itself, all other packages can stay untouched in the installations as long as they are compatible with the new version.
If we would use static instead of shared linkage, we would need to rebuild the packages depending on that library.

Rebuilding packages that depend on a library in the shared linkage setting are limited to the direct dependencies if there was an ABI breakage.
If there was no ABI breakage, there is also no need to rebuild any downstream package.
Users can simply update to the new version.

Rebuilding packages with static linkage is more complicated as the symbols of the library are copied into every binary that uses the library.
Thus there we would not only need to rebuild the packages that directly on the library but all the whole set of downstream packages that also have an indirect linkage as they also may contain copies of the symbols.
As the symbols are copied, we would not only need to do these rebuilds for ABI / version changes but for every new build of the packaged library.

As an additional complication, you also need to include the license of the library in every package that includes symbols of a static library if the license of the library requires it.
This means that executables that are statically linked also need to include the license headers of most of their libraries.

Using dynamic linkage also has the benefit of a smaller disk usage even when the static archives are not installed, 
as we will have the symbols of a library copied into every binary that uses a library.
In the dynamic setting, the symbols would only exist once on disk, inside the shared object.

Not only does dynamic linkage reduce the storage requirements on disk but it also reduces the memory usage when several processes use the same library.
Depending on the operation system, the symbols are then only loaded once into memory and shared between the processes.

Building static archives (in separated packages) is still useful for some non-common workflows.
These don't represent the majority of uses of our packages but can profit a lot from our using our existing infrastructure.

## Implementation

To implement the CFEP, we would remove static libraries from existing packages in new builds.
Quite often static libraries were included by default without the need for them or just because they are the default output.
They are in most cases not used/needed.

If the static libraries are needed though, they should be added as a separate output with a `-static` suffix, see https://github.com/conda-forge/krb5-feedstock/pull/23/files .

Packages that statically link a dependency need to take care that they include the license file of all dependencies that require it.
For now, we will also don't do any automatic rebuilds for packages with static linkage.
This means that packages using static linkage need to regularly rebuild to include the updated versions.
Otherwise the end-user experience will be that the library wasn't updated at all in the stack.

## Windows

We are going to be less strict on Windows builds for static libraries, mainly as the support for builds on Windows is less mature for the majority of tools.
There are a lot libraries that only work with static linkage as the upstreams didn't take care to define the correct `__dllexport/__dllimport` lines.
If a build works fine with shared linkage, it should only providing those.
If a build doesn't yet work with shared linkage on Windows, we shouldn't force the maintainer of the feedstock to write a huge patch to make the linkage work.
There is already a lot of hassle and not that much community support for packaging things on Windows.

One additional caveat on Windows is though that there are several types of static libraries.
In addition to every shared library (`bin\<name>.dll`) there is always accompanying (static) import library (`lib\<name>.lib`).
As the import libraries are required for the linker to work, they definitely need to be included in the default installation.
Sometimes the static build of a library has the same name as the import library, sometimes it comes with a `_static` suffix, i.e. `lib\<name>_static.lib`.
The suffix `.lib` is only used when you are working with the MSVC-based toolchain.
If you use the MinGW-based toolchain, the import library has a `.dll.a` suffix (though import libraries are necessarily needed for MinGW) and the static archives have solely a `.a` suffix, omitting the `.dll` part.

## Reference

Most distributions explicitly forbid the packaging of static libraries or require them to be in non-default / suffixed packages.
The guidelines of [Debian](https://wiki.debian.org/StaticLinking) and [OpenSUSE](https://en.opensuse.org/openSUSE:Shared_library_packaging_policy) are the most informative of them.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
