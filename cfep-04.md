<table>
<tr><td> Title </td><td> Policies for packaging X11-based software in conda-forge </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Peter K. G. Williams &lt;peter@newton.cx&gt; (@pkgw) </td></tr>
<tr><td> Created </td><td> Feb 20, 2017 </td></tr>
<tr><td> Updated </td><td> Feb 27, 2017 </td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>


## Abstract

This CFEP proposes a set of policies for packaging software that uses the X11
graphical interface system. There is currently a set of *de facto* policies,
but they are not written down explicitly. This CFEP aims to remedy this
situation and also codifies a significant change relative to Continuum.io’s
`defaults` channel: a new cross-platform cluster of recipes for the
[X.org](http://www.x.org/) client libraries makes it possible to support
X11-based software more broadly than in `defaults`.


## Current state of play

**Linux**. Linux packages may assume that the system has X11 client libraries
installed. The client libraries are assumed to be compatible with CentOS 6.

**OSX**. Historically, some packages were built assuming that the
[XQuartz](https://www.xquartz.org/) set of X11-related software was available
on the installation system. Currently, packages are not allowed to assume
this, so X11-related functionality is disabled in OSX packages that offer
optional support for it.

**Windows**. To the best of the author’s knowledge, all Windows `conda`
packages have been built without X11 support of any kind.


## Limitations of current situation

The chief problem with the current situation regards scientific software on
OSX. There are various pieces of software that can run usefully on OSX but
require X11 client libraries to be built. Examples include
[pyngl](https://github.com/conda-forge/pyngl-feedstock/pull/3),
[CASA](https://casa.nrao.edu/), and possibly `ncl` and `pyferret` (according
to @ocefpaf). These do not always require an X11 *server* to run, but can have
build systems that are too inflexible to succeed if X11 client libraries
cannot be found and/or linked.

On Linux, the reliance on CentOS-6–compatible system X11 client libraries
could hypothetically be a limitation. CentOS 5 systems have older,
incompatible versions of the X11 libraries, and the current policy precludes
the use of X11 software on extremely minimalist OSes that do not include
system-wide installations of the X11 libraries. However, these situations are
believed to be sufficiently rare at the present day that they may be
neglected.

A lack of X11 client libraries on Windows is not believed to be a serious
limitation to the usefulness of `conda-forge` packages. Software that can be
built on Windows is generally going to have a build system flexible enough
that it won’t require X11. OSX is different — it is close enough to a
traditional Unix system that software can often be compiled on OSX systems
with minimal porting effort, and the early existence of the
[XQuartz](https://www.xquartz.org/) project meant that a non-negligible set of
software packages were adapted to work on OSX under the assumption that X11
client libraries would be available.


## New X.org client library packages

Using the software provided by
[X.org](https://www.x.org/releases/individual/), @pkgw has created conda-forge
recipes for a substantial subset of the X11 client libraries that are
typically found on X11-capable machines. Examples include
[xorg-libx11-feedstock](https://github.com/conda-forge/xorg-libx11-feedstock)
and
[xorg-libxaw3d-feedstock](https://github.com/conda-forge/xorg-libxaw3d-feedstock).
These packages are available on Windows, Linux, and OSX. They therefore
provide the chance for X11-based software to be built against a consistent set
of libraries in all three supported Conda platforms.

It is important to emphasize that X11 is fundamentally a client-server
protocol. By providing these libraries, various pieces of software gain the
capability to talk to an X11 server that they might not otherwise have. The
presence of an X11 server is universally indicated by a non-empty value for
the environment variable `$DISPLAY` in a program’s runtime environment.


## Proposed X11 recipe guidelines

Here we propose candidate guidelines for writing recipes for software that has
the potential to use X11.

### Use of conda-forge X11 packages

*Guideline*. When a conda-forge package links against X11 libraries, in all
but the most exceptional circumstances it should be built against, and link
against, the `xorg-...` packages provided by conda-forge.

*Rationale*. With this guideline we declare that X11 libraries will switch
from being considered a “system” dependency to an internal one. This is done
for the usual reasons: it provides a consistent runtime environment that
allows more conda-forge software to run on a broader set of machines. The most
important example of this broadening is that it becomes possible to run
X11-based software that has been ported to OSX.

*Concerns*. This change distances `conda-forge` packages from `defaults`
packages by add new dependencies on the `xorg-...` packages. This could be
considered a compatibility break. However, downstream compatibility is not
altered in any substantial way. For instance, if `defaults` package `x`
depends on library `liby`, and the `conda-forge` version of `liby` has extra
dependencies on the X.org libraries, the proper libraries will automatically
be loaded by the dynamic linker. The only change is that a few additional
packages are installed relative to the `defaults`-only case.

It is true that these additional dependencies mean that more files must be
downloaded and installed than before. On `linux-64`, the X11 libraries add up
to ~10–20 MB depending on which ones are installed. For comparison, the `hdf5`
package is 30 MB.

One area of concern is the `tk` package, which lies very deep in the `conda`
dependency stack and can use X11 on Linux and OSX. On OSX the current package
in fact
[provides dummy X11 headers that must be superseded by the xorg packages](https://github.com/conda-forge/tk-feedstock/issues/15).
There is rightfully some reluctance to
[make tk depend on the X.org libraries](https://github.com/conda-forge/tk-feedstock/pull/17)
at this time. As the X11 packages become more widely used and we get a sense
of their stability, we may become more comfortable with the idea of pushing
them this deep in the dependency stack.

### Optional X11 dependencies

*Guideline*. For packages that can optionally link to X11, no hard-and-fast
rule can be given as to whether the X11 support should or should not be
enabled. It should be enabled if it enhances the user experience, by allowing
a new piece of software to run, by simplifying conda recipes, or by expanding
the capabilities of a package. It should not be enabled if it degrades the
user experience of one or more conda-forge packages in its normal usage.

The decision that is made need not be the same for all versions of a package.
For instance, it might make sense to enable X11 on the OSX and Linux builds of
a package, but not the Windows build.

*Rationale*. X11 is just a piece of infrastructure: we’re only packaging it to
help people get their work done. If it helps them do that, good; if it’s
hurting, then something has gone wrong.

*Concerns*. Different people have different definitions of what constitutes a
significant degradation, or improvement, of the user experience. For instance,
it is the opinion of the author of this CFEP that downloading an extra 15 MB
of libraries is nothing to worry about, but others might disagree.

### Separable X11 dependencies

*Guideline*. If a package that uses X11 can be split into a main package that
does *not* require X11 and an “add-on” or “plug-in” sub-package that does,
there is a strong preference toward dividing the packages this way. Note that
this guideline will only really come into its own when conda’s support for
[“split” packages](https://github.com/conda/conda-build/issues/1338) is fully
available and documented.

*Rationale*. Although the attitude of this CFEP is that the addition of a
extra dependencies on the `xorg-...` X11 packages is a small price to pay for
functionality that improves the user experience, it is nice to avoid the extra
downloads and installations when possible. Good support for “split” packages
will enable this in a few cases.

*Concerns*. It is unknown how many packages will actually be in a position to
take advantage of this guideline.

Conda’s support for “split” packages is quite new, and it may well take some
time for this feature to attain common usage in conda-forge.


## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
