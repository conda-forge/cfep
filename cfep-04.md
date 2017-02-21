<table>
<tr><td> Title </td><td> Policies for packaging X11-based software in conda-forge </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Peter K. G. Williams &lt;peter@newton.cx&gt; (@pkgw) </td></tr>
<tr><td> Created </td><td> Feb 20, 2017</td></tr>
<tr><td> Updated </td><td> Feb 20, 2017</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>


## Abstract

This CFEP proposes a set of policies for packaging software that uses the X11
graphical interface system. There is currently a set of *de facto* policies,
but they are not written down explicitly. This CFEP aims to remedy this
situation and also codifies a significant change relative to Continuum.io’s
`defaults` channel: a new cross-platform cluster of packages of the
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



## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
