
<table>
<tr><td> Title </td><td> Providing manually uploaded builds on the conda-forge anaconda channel</td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Hadrien Mary &lt;hadrien.mary@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Sept 27, 2016</td></tr>
<tr><td> Updated </td><td> Sept 27, 2016</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Some packages are very hard to build on the currently used CI (CircleCI, Travis and AppVeyor). It's most of the time because of time limitations in the CI website and also because of the complexity of the build.

An example is the Qt5 build that seems to be very hard to build (it tooks almost a year for Continuum folks to compile it for all platforms). See the Qt5 recipe build discussion ticket here https://github.com/conda-forge/qt-feedstock/issues/5.

That being said it's excepted that a small amount of packages will be very hard to automatically build for various reasons. 

Obviously we all want to keep the amount of packages small and have a maximum of automatically build packages on conda-forge.

But a system/protocol to manually upload builds on conda-froge anaconda channel is necessary to circumvent some situations.

## Specification

A protocol to manually upload builds could look something like that :

- The user provides the recipe as a PR.

- The build fails : someone from @conda-forge/core decides to "enable" the manually uploaded builds system.

- People from @conda-forge/core are designated to build, test and upload the package.

For Linux, the `scripts/run_docker_build.sh` should be used (builds are placed in `build_artefacts/`). Building with this script will provide consistency for these packages because they will be build against the same Linux distribution.

On Windows, we could use custom made Vagrant images with all the correct compilers set up. 

On OS X, same comment as before I don't know/use OS X.

We also need a way to differentiate manually uploaded packages from "main" packages. Maybe using different build label `main` and `manual`.

- Test for each platform and different versions the package. The test should be the one already written in the `meta.yaml` file.

For Linux, test at least Debian based distribution and Fedora/CentOS. (Any suggestions for more Linux distribution ?).

For windows, test at least Windows 7 and Windows 10. (do we need Windows 7 ?)

For OS X, we should test against version 10.9 and 10.11.

- Upload the packages.

Note about security : @conda-forge/core people would need to be extremly trusted beccause they are going to build the packages and so could potentially introduce security breach in binary files. An auditing process should be considered (I don't see one at the moment except from trusting people). Also adding the conda recipe to the package is a good practice I think. (Now when you think about it, the situation is not very different from PyPi wheels that contains binary files.)

**Open Ideas/Questions**

- Could we use CI to test manually uploaded packages ? How ?
- Create a separate @conda-forge team dedicated to this task.

## Motivation

Main motivation is to be able to provide the widest range of possible packages in conda-forge. This proposal will be really usefull to upload very hard to build packages.

## Alternatives

A CI infrastructure that build them all !

## FAQ


## Copyright

This document is CC0 1.0 Universal.