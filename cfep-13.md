
<table>
<tr><td> Title </td><td> Securing `conda-forge` Uploads to `anaconda.org` </td>
<tr><td> Status </td><td> Proposed </td></tr>
<tr><td> Author(s) </td><td> Matt, Marius, Anthony, Isuru</td></tr>
<tr><td> Created </td><td> Feb 27, 2020</td></tr>
<tr><td> Updated </td><td> Feb 27, 2020</td></tr>
<tr><td> Discussion </td><td> NA </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

Currently the `anaconda.org` tokens on each feedstock allow any `conda-forge`
feedstock maintainer to upload any output to any `anaconda.org` package.
This could allow someone to push a compromised and/or malicious build
of say the `python` package. This situation is a security risk that we
should mitigate.

## Specification

We will secure the uploads of outputs to `anaconda.org` via the following process.

1. A public, global registry of all outputs from all feedstocks will be kept
   in a repo on github. Write access to this repo is limited to core and bot 
   admins as well.
2. When a feedstock builds new outputs it wants to upload, it will push these outputs
   to a staging organization on `anaconda.org`. These outputs will either be signed 
   so that they can be validated or the feedstock will directly alert the validation 
   service in a secure, verifiable manner.
3. The validation service will validate that any outputs in the staging org are 
   from the correct feedstock, the actual outputs to be uploaded, and allowed 
   for the feedstock (given the global registry).
4. If everything is ok, the outputs are copied from the staging organization to the
   main conda-forge channels. The outputs are then deleted once the copy is done.
5. The service should report a failure to users if an output is not copied.

### The Impact on Users

In order to mitigate the impact on feedstock maintainers, we will

1. Advertise this change widely as it is implemented.
2. PR CI runs should check all of their outputs against the validator 
   in a "dry-run" mode and fail if any output conflicts with some other package.
3. New outputs that don't conflict with any others will be added to the global 
   registry automatically.
   
The above combination of items will let users register new outputs for feedstocks 
in a mostly self-service manner while also not letting them write to an arbitrary 
output.

## Alternatives

There was an effort to support scoped tokens on the `anaconda.org` side. This
would be a simpler way to achieve the same effect. However, this appears to have
not yet happened.

## Reference

See this github [issue](https://github.com/conda-forge/conda-smithy/issues/1061).

## Copyright

This document is [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
