<table>
<tr><td> Title </td><td> Security and Systems Subteam </td>
<tr><td> Status </td><td> Draft </td></tr>
<tr><td> Author(s) </td><td> Matthew R. Becker &lt;becker.mr@gmail.com&gt;</td></tr>
<tr><td> Created </td><td> Mar 7, 2020</td></tr>
<tr><td> Updated </td><td> Mar 7, 2020</td></tr>
<tr><td> Discussion </td><td> https://github.com/conda-forge/cfep/pull/25 </td></tr>
<tr><td> Implementation </td><td> NA </td></tr>
</table>

## Abstract

The purpose of the security and systems subteam is to secure and maintain appropriate access 
to the credentials and services/systems used by `conda-forge`. This infrastructure 
includes all bot accounts, all service provider accounts, and all keys, API or otherwise, 
used for various tasks. This team is also charged with provisioning new members of `core` 
with access to the organization.


## Motivation

`conda-forge` maintains a lot of infrastructure both in terms of code but also
in terms of bot accounts, API keys for various services, and accounts on various 
services (e.g., GitHub, DockerHub, etc.). This infrastructure has grown organically 
over time to accommodate new needs and goals of the organization. 

While nothing is a priori wrong with this mode of operation, it does limit our ability 
to perform basic security tasks, like rotating API keys. It also makes it difficult to 
provision new members of `core` with access to the organization. Finally, should parts 
of the `conda-forge` infrastructure be lost or compromised, we currently have no 
procedures in place to recover it. 

## Specification

The core responsibilities of this team are to

- maintain secure access to the credentials to all bot accounts
- maintain secure access to all API keys used by `conda-forge`
- maintain secure access to all third-party provider accounts owned by `conda-forge`
- maintain the infrastructure for CFEP-13
- develop automated ways to recover and reprovision `conda-forge` systems
- perform ongoing security maintenance tasks like rotating API keys

Only members of `core` can be on this subteam.

## Copyright

All CFEPs are explicitly [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).
