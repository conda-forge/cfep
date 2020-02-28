
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

1. Each feedstock is provisioned with a secret key that is only known to the
   maintainers of that feedstock and the core team. These keys will be kept in
   a private repo with only core members having write access. This key will be pushed to
   the CI providers for that repo as well.
2. Feedstock creation will move to a separate process in order to secure the keys
   associated with our CI provider accounts and to securely register outputs of feedstocks.
3. A public, global registry of all outputs from all feedstocks will be kept
   in a repo on github. Write access to this repo is limited to core as well.
4. When a feedstock builds new outputs it wants to upload, it will push these outputs
   to a staging organization on `anaconda.org`. It will also send the names of the
   outputs, their SHA256 checksums, the feedstock name, and the secret key to a
   validation web service. This web service then verifies the secret key-feedstock
   combination and verifies that the outputs are allowed for that feedstock.
5. If everything is ok, the outputs are copied from the staging organization to the
   main conda-forge channels.
6. The service should fail the CI run if the output is not copied. It should 
   also post a comment on the PR that the outputs were copied or not to help users.


### The Impact on Users

In order to mitigate the impact on feedstock maintainers, we will

1. Advertise this change widely as it is implemented.
2. Create an admin bot command to create a PR for new registering new outputs.
3. PR CI runs should check all of their outputs against the web service in a "dry-run"
   mode and fail if any output is not registered.
4. PRs for registering new outputs will be automatically merged if they 
   don't overlap with any existing name on any feedstock.
   
The above combination of items will let users register new outputs for feedstocks 
in a mostly self-service manner while also not letting them write to any output.

## Alternatives

There was an effort to support scoped tokens on the `anaconda.org` side. This
would be a simpler way to achieve the same effect. However, this appears to have
not yet happened.

## Reference

See this github [issue](https://github.com/conda-forge/conda-smithy/issues/1061).

## Copyright

This document is [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
