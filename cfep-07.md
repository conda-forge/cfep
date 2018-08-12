
<table>
<tr><td> Title </td><td> Migration strategy to Anaconda compilers </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Marius van Niekerk &lt;@mariusvniekerk&gt;</td></tr>
<tr><td> Created </td><td> Aug 8, 2018</td></tr>
<tr><td> Updated </td><td> Aug 8, 2018</td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/conda-forge-enhancement-proposals/pull/10 </td></tr>
<tr><td> Implementation </td><td> (listed below) </td></tr>
</table>

## Abstract

As part of the move to the Anaconda compilers we need to replace some parts of our tooling whilst not
affecting the rest of the conda-forge build stack.

To do this we will publish to two labels (main and gcc7)

## Implementation

Implmentation of this is done in phases to avoid breaking the majority of the conda forge stack.

As part of this we are introducing a few system keys into conda-forge-pinning

*   channel_sources

    This is a list of channels that are allowed to be used as sources for building conda-forge packages
    
    ```yaml
    channel_sources:
    - conda-forge,defaults              # [compiler_label < 7]
    - conda-forge/label/gcc7,defaults   # [compiler_label >= 7]
    ```

*   channel_targets

    This is a list of channels and labels that packages should be published to:
    
    ```yaml
    channel_targets:
    - conda-forge main   # [compiler_label < 7]
    - conda-forge gcc7   # [compiler_label >= 7]
    ```

*   build_number_decrement
    In order to ensure that post label merge we can have both sets of packages co-existing together
    in the same label, we will decrement the build number for packages built with the older compilers
    so that the new ones will have higher solver priority.

    ```yaml
    build_number_decrement:
    - 1000              # [compiler_label < 7]
    - 0                 # [compiler_label >= 7]
    ```


### Phase 1
*   Adapt `conda-smithy` so that it can emit the required configurations for both compilers. 

    [PR: conda-smithy#836](https://github.com/conda-forge/conda-smithy/pull/836)

*   Adapt `conda-force-ci-setup` so that it can more easily be used to differentiate between compiler variants

    [PR: conda-forge-ci-setup#19](https://github.com/conda-forge/conda-forge-ci-setup-feedstock/pull/19)

*   Adapt `conda-forge-pinnings` to refer to the new compilers and the new label

    [PR: conda-forge-pinning-feedstock#92](https://github.com/conda-forge/conda-forge-pinning-feedstock/pull/92)

    This cannot be merged to master and released until Phase 3

### Phase 2

*   Create a migrator using the conda-forge bot infrastructure on regro to carry out performing the 
    rebuild migrations.

    This migrator will use the conda-smity and conda-forge-pinnings from Phase 1

*   Run the migrator against the stack.  This will take several months to get to completion.
    `conda-forge/core` will monitor the progress of the migration and vote on when to move to phase 3

### Phase 3

*   Unify the two labels under the same one

*   Label pre-gcc7 artifacts as pre-gcc7.  

*   Move gcc7 artifacts into main label.

*   Remove support for building with toolchain_X

## Rationale

*   There is an increasing large ABI incompatible gulf between `defaults` and `conda-forge`.
    In order to prevent user confusion and frustration it is best if we were more closely aligned with 
    AnacondaRecipes

*   Our existing compilers are very old and don't allow compilation against C++17 or other more modern
    C++ features

## Backward Compatibility

*   This is a backwards incompatible change for ALL packages that are recompiled.
*   We will not place the new packages in the main channel until the final phase of implementation.

## References



## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
